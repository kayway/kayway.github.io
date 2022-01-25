---
title: "BurnoutFX Mod for GTA5(FiveM)"
header:
  video:
    id: q_70TkdGUa4
    provider: youtube
categories:
  - Mods
tags:
  - SQL
  - C#
  - .NET
toc: true
toc_label: "Contents"
toc_sticky: true
---
**Note:** Project Currently Ongoing
{: .notice}

[Project Code](https://github.com/kayway/BurnoutFX)

# Overview
This mod intends to merge the Gameplay of GTA5 with mechanics and systems from other popular racing games like Burnout Paradise.
This mod interacts with a SQL server and records player statistics and race track data. 

# Features
- Stunt Points system - get points by drifting, air time and slipstreaming other vehicles.
- Partake in timetrials that record your best times for others to beat.
- Race with others and make your own races.

# Details
## SQL database

To use MySQL with my project I used a NuGet Package "[MySqlConnector](https://mysqlconnector.net)".
This provides some useful utilities for connecting and submitting queries to the database as shown bellow.

### Getting Available Tracks
**Tracks Table**
The "start" and "checkpoints" collumns contain JSON data to be parsed through the script.
```sql
CREATE TABLE `tracks` (
	`title` VARCHAR(50) NOT NULL COLLATE 'utf8mb4_general_ci',
	`colour` INT(3) UNSIGNED NOT NULL DEFAULT '5',
	`icon` INT(3) UNSIGNED NOT NULL DEFAULT '315',
	`start` LONGTEXT NULL DEFAULT NULL COLLATE 'utf8mb4_bin',
	`checkpoints` LONGTEXT NULL DEFAULT NULL COLLATE 'utf8mb4_bin',
	`radius` FLOAT NOT NULL DEFAULT '24',
	`transparency` FLOAT NOT NULL DEFAULT '1',
	`enabled` BIT(1) NOT NULL DEFAULT b'1',
	PRIMARY KEY (`title`) USING BTREE
)
```
First we Request the Data asynchronously at the initial runtime of the script and store it in a variable.
**GameManager.cs**
```c#
//Contains the start markers for all tracks in the format track name, marker data.
public static Dictionary<string, Tuple<uint, uint, string>> MarkerData = new Dictionary<string, Tuple<uint, uint, string>>();

private static async void GetGameMarkers()
{
  MarkerData = await DatabaseConnector.RetrieveTracks();
  Debug.WriteLine(MarkerData.Count.ToString());
}
```
**DatabaseConnector.cs**
```c#
/// <summary>
/// Queries the Tracks database.
/// </summary>
/// <returns>Enabled Tracks Marker Data.</returns>
public static async Task<Dictionary<string, Tuple<uint, uint, string>>> RetrieveTracks()
{
  DatabaseApp database = new DatabaseApp();
  Dictionary<string, Tuple<uint, uint, string>> trackMarkers = new Dictionary<string, Tuple<uint, uint, string>>();
  try
  {
    await database.Connection.OpenAsync();
    MySqlCommand command = new MySqlCommand("SELECT title, colour, icon, start FROM tracks WHERE enabled=1", database.Connection);
    MySqlDataReader reader = command.ExecuteReader();
    while (reader.Read())
    {
      trackMarkers.Add(reader.GetString(0), Tuple.Create(reader.GetUInt32(1), reader.GetUInt32(2), reader.GetString(3)));
      Debug.WriteLine("name: " + reader[0] + " marker: " + reader[3]);
    }
      reader.Close();
  }
    catch (Exception exception)
    {
      Debug.WriteLine($"Oh This happened: >> {exception} ");
    }
    return trackMarkers;
}
```

### Sending the Data to the Client
Then we create a event listener which awaits a request event from the client.
Due to the limitations of the type of data that can be transferred, we make us of the "[Newtonsoft.Json](https://www.newtonsoft.com/json)" package.
This package allows us to serialize/convert Data to a string in JSON format to deserialize on the client.
**GameManager.cs**
```c#
[EventHandler("RequestMarkerData")]
private static void SendGameMarkers([FromSource]Player player)
{
  string trackMarkerData = JsonConvert.SerializeObject(MarkerData);
  string gameMarkerData = JsonConvert.SerializeObject(availableGameMarkers);
  TriggerClientEvent(player, "RetrieveMarkerData", trackMarkerData, gameMarkerData);
}
```
The request event is sent from the client at runtime and then with an even listener, waits for the data to be sent.
Once the data is retrieved we then iterate through the dictionary to convert the marker data into a marker object to process later.
**ClientManager.cs**
```c#
/// <summary>
/// Processes requested marker data and converts them from JSON
/// </summary>
/// <param name="trackMarkerData"></param>
/// <param name="gameMarkerData"></param>
[EventHandler("RetrieveMarkerData")]
private void ParseMarkerData(string trackMarkerData, string gameMarkerData)
{
  Dictionary<string, Tuple<uint, uint, string>> trackMarkers = JsonConvert.DeserializeObject<Dictionary<string, Tuple<uint, uint, string>>>(trackMarkerData);
  foreach(KeyValuePair<string, Tuple<uint, uint, string>> kvp in trackMarkers)
  {
    string markerName = kvp.Key;
    int blipColour = (int) kvp.Value.Item1;
    int blipIcon = (int) kvp.Value.Item2;
    Marker marker = JsonConvert.DeserializeObject<Marker>(kvp.Value.Item3);
    int blipID = marker.AddBlip(markerName, blipColour, blipIcon);
    blips.Add(blipID);
    TrackMarkers.Add(markerName, marker);
  }
  Dictionary<string, string> gameMarkers = JsonConvert.DeserializeObject<Dictionary<string, string>>(gameMarkerData);
  foreach (KeyValuePair<string, string> kvp in gameMarkers)
  {
    Marker marker = JsonConvert.DeserializeObject<Marker>(kvp.Value);
    int blipID = marker.AddBlip(kvp.Key, 255, 255);
    blips.Add(blipID);
    GameMarkers.Add(kvp.Key, marker);
  }
}
```

## Determining Whether a Car is Drifting
This was a bit more fun to code as it involves looking at some math.
We need to take the velocity, sine and cosine of the car and multiply them with the respective velocity.
This gives us the angle of the car with the given direction but we should also include a division by magnitude.
**StuntCounter.cs**
```c#
private bool isDrifting()
{  
  Vehicle vehicle = Game.PlayerPed.CurrentVehicle;
  if (vehicle == null)
    return false;
  //whether the car has started or speed is below 30km/h
  if (vehicle.CurrentGear == 0 || vehicle.Speed * 3.6 < 30)
    return false;
  Vector3 velocity = vehicle.Velocity;
  double magnitude = Math.Sqrt(velocity.X * velocity.X + velocity.Y * velocity.Y);
  Vector3 vehicleRot = vehicle.Rotation;
  double sin1 = -Math.Sin(degrees2Radians(vehicleRot.Z));
  double cos1 = Math.Cos(degrees2Radians(vehicleRot.Z));
  double cosX = (sin1 * velocity.X + cos1 * velocity.Y) / magnitude;
  //angle tolerance
  if (cosX > 0.966 || cosX < 0)
    return false;   
  return true;
}

private double degrees2Radians(double degrees)
{
  return ((Math.PI / 180) * degrees);
}    
```
## Racing/Timetrials
### Checkpoint Functionality
This function is used for both Timetrials and races.
This uses the parsed track data from earlier and iterates through the checkpoint when the player reaches the radius of the checkpoint. 
**Race.cs**
```c#
private async void startRacing(int startTime)
{
  TriggerServerEvent("StartedGame");
  int checkpointID = 0;
  Marker currentCheckpoint = TrackData[checkpointID], nextCheckpoint = TrackData[checkpointID + 1], previousCheckpoint = TrackData[checkpointID - 1];
  int checkpointHandle = CreateCheckpoint(currentCheckpoint.Type, currentCheckpoint.X, currentCheckpoint.Y, currentCheckpoint.Z,
    nextCheckpoint.X, nextCheckpoint.Y, nextCheckpoint.Z, CheckpointRadius, 255, 255, 0, 127, 0);
  SetCheckpointCylinderHeight(checkpointHandle, 1, 10, 10);
  int checkpointBlip = AddBlipForCoord(currentCheckpoint.X, currentCheckpoint.Y, currentCheckpoint.Z);
  SetNewWaypoint(currentCheckpoint.X, currentCheckpoint.Y);
  while (CurrentBPlayer.State == PlayerState.InGame)
  {  
    float drawTime = (float)Game.GameTime - (float)startTime;
    Vector3 queryVector = new Vector3(currentCheckpoint.X, currentCheckpoint.Y, currentCheckpoint.Z);
    float checkpointDistance = Game.PlayerPed.Position.DistanceToSquared(queryVector);
    DrawGameText((drawTime / 1000).ToString(), 0.1f, 0.025f, 0, 238, 198, 78, 255, 0.7f, 0.7f);
    string checkpointText = string.Format("Checkpoint {0} / {1} ({2:F3} m)", checkpointID, TrackData.Length, checkpointDistance / 1000);
    DrawGameText(checkpointText, 0.1f, 0.065f, 0, 238, 198, 78, 255);
    DrawGameText(checkpointID.ToString(), 0.5f, 0.5f);
    if (Game.PlayerPed.Position.DistanceToSquared(queryVector) < CheckpointRadius)
    {
      DeleteCheckpoint(checkpointHandle);
      RemoveBlip(ref checkpointBlip);
      if (checkpointID +1 == TrackData.Length) //Checks if the next checkpoint is the last one
      {
        PlaySoundFrontend(-1, "ScreenFlash", "WastedSounds", true);
        string vehicleName = Game.PlayerPed.CurrentVehicle.LocalizedName;
        if (vehicleName.Contains("NULL"))
          vehicleName = Game.PlayerPed.CurrentVehicle.DisplayName;
        TriggerServerEvent("FinishedGame", Game.GameTime - startTime, GetLabelText(vehicleName));
        CurrentBPlayer.State = PlayerState.None;
      }
      else
      {
        checkpointID++;
        currentCheckpoint = TrackData[checkpointID];
        nextCheckpoint = TrackData[checkpointID + 1];
        PlaySoundFrontend(-1, "RACE_PLACED", "HUD_AWARDS", true);
        checkpointHandle = CreateCheckpoint(currentCheckpoint.Type, currentCheckpoint.X, currentCheckpoint.Y, currentCheckpoint.Z,
        nextCheckpoint.X, nextCheckpoint.Y, nextCheckpoint.Z, CheckpointRadius, 255, 255, 0, 127, 0);
        SetCheckpointCylinderHeight(checkpointHandle, 1, 10, 10);
        checkpointBlip = AddBlipForCoord(currentCheckpoint.X, currentCheckpoint.Y, currentCheckpoint.Z);
        SetNewWaypoint(currentCheckpoint.X, currentCheckpoint.Y);
      }
    }
    await Delay(0);
  }
}
```
### Submitting Finish times
**Timetrials Table**
```sql
CREATE TABLE `timetrials` (
	`id` INT(10) UNSIGNED NOT NULL AUTO_INCREMENT,
	`track` VARCHAR(100) NOT NULL COLLATE 'utf8mb4_general_ci',
	`time` INT(10) UNSIGNED NOT NULL,
	`vehicle` VARCHAR(100) NULL DEFAULT 'Car Not Found' COLLATE 'utf8mb4_general_ci',
	`player_id` VARCHAR(100) NOT NULL COLLATE 'utf8mb4_general_ci',
	PRIMARY KEY (`id`) USING BTREE
)
```
Once a race is finished, if it is a timetrial it will call the function bellow.
This function compares the player's time with multiple Queries to determine whether the player has one of the following:
- Best time for the track.
- Best time in the player's car.
- Best personnel time.
- First time on this track.
If one of the listed conditions is met, the player's details and time are submitted to the database.
**DatabaseConnector.cs**
```c#
/// <summary>
/// Submit the player's time for the track.
/// </summary>
/// <param name="playerID"></param>
/// <param name="time"></param>
/// <param name="track"></param>
/// <param name="vehicle"></param>
public static async void SubmitTime(string playerID, int time, string track, string vehicle)
{
  DatabaseApp database = new DatabaseApp();
  try
  {
    await database.Connection.OpenAsync();
    MySqlCommand bestTrackCommand = new MySqlCommand("SELECT MIN(time) FROM timetrials WHERE track = @track", database.Connection);
    bestTrackCommand.Parameters.AddWithValue("@track", track);
                
    MySqlCommand bestCarCommand = new MySqlCommand("SELECT MIN(time) FROM timetrials WHERE track = @track AND vehicle = @vehicle", database.Connection);
    bestCarCommand.Parameters.AddWithValue("@track", track);
    bestCarCommand.Parameters.AddWithValue("@vehicle", vehicle);
                
    MySqlCommand bestPersonalCommand = new MySqlCommand("SELECT MIN(time) FROM timetrials WHERE track = @track AND player_id = @player_id", database.Connection);
    bestPersonalCommand.Parameters.AddWithValue("@player_id", playerID);
    bestPersonalCommand.Parameters.AddWithValue("@track", track);
                
    MySqlCommand submitCommand = new MySqlCommand("REPLACE INTO timetrials (track, vehicle, player_id, time) VALUES(@track, @vehicle, @player_id, @finish)", database.Connection);
    submitCommand.Parameters.AddWithValue("@player_id", playerID);
    submitCommand.Parameters.AddWithValue("@track", track);
    submitCommand.Parameters.AddWithValue("@finish", time);
    submitCommand.Parameters.AddWithValue("@vehicle", vehicle);
                
    uint bestTrackTime = ConvertFromDBVal<uint>(bestTrackCommand.ExecuteScalar());
    if (bestTrackTime != 0)
    {              
      Debug.WriteLine(bestTrackTime.ToString());
      if(bestTrackTime > time)
      {
        Debug.WriteLine("Best time on track!!");
        submitCommand.ExecuteNonQuery();
      }
      else
      {
        uint bestCarTime = ConvertFromDBVal<uint>(bestCarCommand.ExecuteScalar());
        if (bestCarTime == 0 || bestCarTime > time)
        {
          Debug.WriteLine("Best Time in this car!!");
          submitCommand.ExecuteNonQuery();
        }
        else
        {
          uint bestPersonalTime = ConvertFromDBVal<uint>(bestPersonalCommand.ExecuteScalar());
          if (bestPersonalTime == 0 || bestPersonalTime > time)
          {
            Debug.WriteLine("Best personal time!!");
            submitCommand.ExecuteNonQuery();
          }
          else
          {
            Debug.WriteLine("You tried...");
          }
        }
      }
    }
    else
    {
      Debug.WriteLine("First time on this track!!");
      submitCommand.ExecuteNonQuery();
    }            
  }
  catch (Exception exception)
  {
    Debug.WriteLine($"Oh This happened: >> {exception} ");
  }
}
```

### Networking
Whenever the player starts a game it creates a new ActiveGame object.
This stores some important references for other players to use to get the right data.
The ActiveGames are stored in a dictionary along with a list of participants.
**GameManager.cs**
```c#
public static Dictionary<ActiveGame, List<Player>> activeGames = new Dictionary<ActiveGame, List<Player>>();

/// <summary>
/// Requests Track Data and creates a joinable game
/// </summary>
/// <param name="player"></param>
/// <param name="gameData"></param>
/// <param name="markerData"></param>
[EventHandler("CreateGame")]
public async void CreateGame([FromSource] Player player, string gameData, string markerData = "")
{            
  ActiveGame newGame = JsonConvert.DeserializeObject<ActiveGame>(gameData);
  List<Player> players = new List<Player>(1);
  players.Add(player);
  newGame.GameID = activeGames.Count + 1;
  activeGames.Add(newGame, players);
  var trackData = await DatabaseConnector.RetreiveTrackData(newGame.TrackName);
  TriggerClientEvent(player, "retreiveGameData", newGame.GameID, trackData.Item1, trackData.Item2, trackData.Item3);
  if (newGame.Mode != GameMode.timetrial)
  {
    availableGameMarkers.Add(newGame.GameName, markerData);
    Debug.WriteLine(markerData);
    TriggerClientEvent("RetreiveNewGameMarker", newGame.GameName, markerData);
  }
}
```
**ActiveGame.cs**
```c#
public enum GameMode
{
  race,
  timetrial,
  demolition
}    
public class ActiveGame
{
  public int GameID { get; set; }
        
  public string TrackName { get; set; }
        
  // Usually name of the player and track unless its a timetrail then its the same as TrackName
  public string GameName { get; set; }
        
  // Time to start the game, by default is 10 seconds (10000ms).
  public int StartTime { get; set; } = 10000;

  public GameMode Mode { get; set; }
        
  public ActiveGame(string name, GameMode mode, int startTime)
  {
    TrackName = name;
    Mode = mode;
    StartTime = startTime;
  }
}
```
Once a game has been created the active game is sent to all other players and are available to join.
**GameManager.cs**
```c#
/// <summary>
/// Adds the player to participants in active game and sends game data
/// </summary>
/// <param name="player"></param>
/// <param name="markerName"></param>
[EventHandler("JoinGame")]
public async void JoinGame([FromSource] Player player, string markerName)
{
  string gameName = "";
  foreach (KeyValuePair<ActiveGame, List<Player>> kvp in activeGames)
  {
    if (kvp.Key.GameName == markerName)
    {
      gameName = kvp.Key.GameName;
      kvp.Value.Add(player);
      var trackData = await DatabaseConnector.RetrieveTrackData(kvp.Key.TrackName);
      TriggerClientEvent(player, "retrieveGameData", kvp.Key.GameID, trackData.Item1, trackData.Item2, trackData.Item3);
      break;
    }
  }
}
```

# Final Thoughts
FiveM is a great way to get your hands dirty with all sorts of programming languages and coding Gameplay mechanics without the need for developing other assets.
This mod is still in early development and theres many more features I wish to implement, but even so I've learnt so much already.