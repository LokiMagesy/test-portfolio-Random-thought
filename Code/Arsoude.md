```csharp
if (listHike == null)
{
    throw new ArgumentNullException(nameof(listHike), "List of hikes cannot be null.");
}
```

```csharp
public List<Hike> GetListByTerme(string terme, List<Hike> listHike)
{
    string[] searchTerms = terme.Split(' ');
    // HashSet<Hike> uniqueHikes = new HashSet<Hike>(listHike.Count);
    List<Hike> filteredList = new List<Hike>();

    foreach (string term in searchTerms)
    {
        AddMatchingHikes(filteredList, listHike, x => x.Name.ToUpper().Contains(term));
        AddMatchingHikes(filteredList, listHike, x => x.Description.ToUpper().Contains(term));
    }

    return filteredList;
}
```

```csharp
private void AddMatchingHikes(List<Hike> filteredList, List<Hike> listHike, Func<Hike, bool> condition)
{
    var matches = listHike.Where(condition).Except(filteredList).ToList();

    if (matches.Count == 1)
    {
        filteredList.Add(matches[0]);
    }
    else if (matches.Count > 1)
    {
        filteredList.AddRange(matches);
    }
}
```

```csharp
public FiltreServiceTest()
    {
        options = new DbContextOptionsBuilder<ArsoudeContext>()
            .UseInMemoryDatabase(databaseName: "FiltreServiceTest")
            .Options;
        _context = new ArsoudeContext(options);
        _hikeService = new HikeService(_context);
        _filtreService = new FiltreService(_context);
    }

    [TestMethod]
    public void FiltreService_GetFilteredHikesByName_ShouldReturnFilteredResults()
    {
        // Arrange
        List<Hike> hikes = new List<Hike>
        {
            new Hike { Name = "Mountain Trail", Description = "Enjoy a scenic mountain trail" },
            new Hike { Name = "Lake View", Description = "Explore the beautiful lake view" },
            new Hike { Name = "Forest Adventure", Description = "Thrilling adventure in the dense forest" },
            new Hike { Name = "River Expedition", Description = "Exciting expedition along the river" },
        };

        foreach (var hike in hikes)
        {
            _hikeService.AddHike(hike);
        }

        // Act
        List<Hike> result = _filtreService.GetFilteredHikesByName("Mountain Trail");

        // Assert
        Assert.AreEqual(1, result.Count);
        Assert.AreEqual("Mountain Trail", result[0].Name);
    }

    [TestMethod]
    public void FiltreService_GetFilteredHikesByDescription_ShouldReturnFilteredResults()
    {
        // Arrange
        List<Hike> hikes = new List<Hike>
        {
            new Hike { Name = "Mountain Trail", Description = "Enjoy a scenic mountain trail" },
            new Hike { Name = "Lake View", Description = "Explore the beautiful lake view" },
            new Hike { Name = "Forest Adventure", Description = "Thrilling adventure in the dense forest" },
            new Hike { Name = "River Expedition", Description = "Exciting expedition along the river" },
        };

        foreach (var hike in hikes)
        {
            _hikeService.AddHike(hike);
        }

        // Act
        List<Hike> result = _filtreService.GetFilteredHikesByDescription("dense");

        // Assert
        Assert.AreEqual(1, result.Count);
        Assert.AreEqual("Forest Adventure", result[0].Name);
    }

    [TestMethod]
    public void FiltreService_GetFilteredHikesByTerme_ShouldReturnFilteredResults()
    {
        // Arrange
        List<Hike> hikes = new List<Hike>
        {
            new Hike { Name = "Mountain Trail", Description = "Enjoy a scenic mountain trail" },
            new Hike { Name = "Lake View", Description = "Explore the beautiful lake view" },
            new Hike { Name = "Forest Adventure", Description = "Thrilling adventure in the dense forest" },
            new Hike { Name = "River Expedition", Description = "Exciting expedition along the river" },
        };

        foreach (var hike in hikes)
        {
            _hikeService.AddHike(hike);
        }

        // Act
        List<Hike> result = _filtreService.GetFilteredHikesByTerme("Mountain dense");

        // Assert
        Assert.AreEqual(2, result.Count);
        Assert.IsTrue(result.Exists(h => h.Name == "Mountain Trail"));
        Assert.IsTrue(result.Exists(h => h.Name == "Forest Adventure"));
    }
```
---
# Type
```csharp
public List<Hike> GetListByType(EnumHikeType? type, List<Hike> listHike)
{
    return listHike.Where(x => x.Type == type).ToList();
}
```

```csharp
    [TestMethod]
    public void GetListByType_ShouldReturnFilteredHikes()
    {
        // Arrange
        EnumHikeType? targetType = EnumHikeType.Walk;  // Change to the desired type
        List<Hike> hikes = new List<Hike>
        {
            new Hike { Id = 1, Name = "Walking Trail", Type = EnumHikeType.Walk },
            new Hike { Id = 2, Name = "Bike Path", Type = EnumHikeType.Bike },
            new Hike { Id = 3, Name = "Mountain Hike", Type = EnumHikeType.Mountain }, // This one has a different type
            // Add more sample hikes with different types
        };
        List<Hike> emptyList = new List<Hike>();

        HikeService hikeService = new HikeService();  // Initialize your HikeService or use dependency injection

        // Act
        List<Hike> filteredHikes = hikeService.GetListByType(targetType, hikes);
		List<Hike> filteredEmptyHikes = hikeService.GetListByType(targetType, emptyList);

        // Assert
        Assert.IsNotNull(filteredHikes);
        Assert.IsNotHull(filteredEmptyHikes);
        Assert.AreEqual(0, filteredEmptyHikes.Count);

        // Check only for the specified type (Walk or Bike)
        foreach (var hike in filteredHikes)
        {
            Assert.AreEqual(targetType, hike.Type);
        }
        
	    CollectionAssert.AreEqual(hikes, filteredHikes);  // Check that the original list is returned
    }
```
---
```csharp
public double DegreesToRadians(double degrees)
{
    return degrees * (Math.PI / 180.0);
}

public double DistanceInMetersBetweenEarthCoordinates(double lat1, double lon1, double lat2, double lon2)
{
    const double EarthRadiusKm = 6371.0;

    double dLat = DegreesToRadians(lat2 - lat1);
    double dLon = DegreesToRadians(lon2 - lon1);

    lat1 = DegreesToRadians(lat1);
    lat2 = DegreesToRadians(lat2);

    double a = Math.Sin(dLat / 2.0) * Math.Sin(dLat / 2.0) +
               Math.Sin(dLon / 2.0) * Math.Sin(dLon / 2.0) * Math.Cos(lat1) * Math.Cos(lat2);
    double c = 2.0 * Math.Atan2(Math.Sqrt(a), Math.Sqrt(1.0 - a));

    return EarthRadiusKm * c * 1000.0; // Return distance in meters
}
```

---
# Distance
``` csharp
public List<Hike> GetListByDistance(int distance, List<Hike> listHike)
        {
            if (distance < 0)
            {
                throw new ArgumentException("Distance must be a non-negative value.", nameof(distance));

            }

            List<Hike> listfilter = new();
            foreach (Hike item in listHike)
            {

                double distanceCalc = distanceInKmBetweenEarthCoordinates(item.StartPointLat, item.StartPointLong, item.EndPointlat, item.EndPointLong);

                switch (distance) {
                    case 0:
                        if (distanceCalc >= 0 && distanceCalc <= 10000) // 10 Km
                            listfilter.Add(item);
                        break;
                    case 1:
                        if (distanceCalc > 10000 && distanceCalc <= 25000) // 25 Km
                            listfilter.Add(item);
                        break;
                    case 2:
                        if (distanceCalc > 25000 && distanceCalc < 50000) // 50 Km
                            listfilter.Add(item);
                        break;
                    case 3:
                        if (distanceCalc > 50000)
                            listfilter.Add(item);
                        break;
                    default:
                        throw new ArgumentException("Invalid distance level.", nameof(distance));
                }
            }
            return listfilter;

        }
```
---
# Km
```csharp
public async Task<List<Hike>> GetListByKm(int? distance, List<Hike> listHike, User user)
{
    if (user == null)
    {
        throw new Exception("InexistentUser");
    }

    List<Hike> listfilter = new List<Hike>();
    List<double> codePostalLocation = await GetLocationAsync(user.PostalCode);

    double userLat = codePostalLocation[0];
    double userLng = codePostalLocation[1];

    foreach (Hike item in listHike)
    {
        double hikeDistance = distanceInKmBetweenEarthCoordinates(item.StartPointLat, item.StartPointLong, userLat, userLng);

        switch (distance)
        {
            // 10 Km
            case 0:
                if (hikeDistance <= 5)
                {
                    listfilter.Add(item);
                }
                break;
            // 25 Km
            case 1:
                if (hikeDistance <= 15)
                {
                    listfilter.Add(item);
                }
                break;
            // 50 Km
            case 2:
                if (hikeDistance <= 25)
                {
                    listfilter.Add(item);
                }
                break;
            case 3:
                if (hikeDistance > 25)
                {
                    listfilter.Add(item);
                }
                break;
            default:
                break;
        }
    }

    return listfilter;
}
```
---
# Controller

```csharp
[HttpPost]
public async Task<ActionResult<List<Hike>>> FiltreHike(Filtre value)
{
    try
    {
        if (value is null)
            return NotFound("BadFiltre");

        if (IsFilterEmpty(value))
            return NotFound("FiltreVide");

        List<Hike> listHike = await _hikeService.GetAll() ?? new List<Hike>();
        listHike = listHike.Where(hike => hike.IsAccepted).ToList();
// List<Hike> listHike = (await _hikeService.GetAll())?.Where(hike => hike.IsAccepted).ToList() ?? new List<Hike>();

        if (!value.Terme.IsNullOrEmpty())
            listHike = _filtreService.GetListByTerme(value.Terme.ToUpper().Trim(), listHike);

        if (value.Type != null)
            listHike = _filtreService.GetListByType(value.Type, listHike);

        if (UserId != null)
        {
            User currentUser = await _userService.GetUserById(UserId) ?? throw new Exception("L'utilisateur n'existe pas");

            listHike = listHike.Where(hike => hike.IsAccepted || hike.UserId == UserId).ToList();

            if (value.MyHike != false)
                listHike = listHike.Where(x => x.UserId == UserId).ToList();

            listHike = value.Km != null
                ? await _filtreService.GetListByKm(value.Km, listHike, currentUser)
                : await _filtreService.GetListByOrderOfLocation(currentUser, listHike);
        }

        return Ok(listHike);
    }
    catch (Exception ex)
    {
        return StatusCode(500, $"Une erreur s'est produite lors du filtre: {ex.Message}");
    }
}

private bool IsFilterEmpty(Filtre value)
{
    return value.Terme.IsNullOrEmpty() && value.Type == null && value.Distance == null && value.Km == null && value.MyHike == null;
}
```


```csharp
[HttpPost]
public async Task<ActionResult<List<Hike>>> FiltreHike(Filtre value)
{
    try
    {
        if (value is null)
            return NotFound("BadFiltre");

        if (IsFilterEmpty(value))
            return NotFound("FiltreVide");

        List<Hike> listHike = await GetFilteredHikes(value);

        return Ok(listHike);
    }
    catch (Exception ex)
    {
        return StatusCode(500, $"Une erreur s'est produite lors du filtre: {ex.Message}");
    }
}

private async Task<List<Hike>> GetFilteredHikes(Filtre value)
{
    List<Hike> listHike = await _hikeService.GetAllAcceptedHikes() ?? new List<Hike>();

    if (!value.Terme.IsNullOrEmpty())
        listHike = _filtreService.GetListByTerme(value.Terme.ToUpper().Trim(), listHike);

    if (value.Type != null)
        listHike = _filtreService.GetListByType(value.Type, listHike);

    if (UserId != null)
    {
        User currentUser = await _userService.GetUserById(UserId) ?? throw new Exception("L'utilisateur n'existe pas");

        listHike = listHike.Where(hike => hike.IsAccepted || hike.UserId == UserId).ToList();

        if (value.MyHike != false)
            listHike = listHike.Where(x => x.UserId == UserId).ToList();

        listHike = value.Km != null
            ? await _filtreService.GetListByKm(value.Km, listHike, currentUser)
            : await _filtreService.GetListByOrderOfLocation(currentUser, listHike);
    }

    return listHike;
}

private bool IsFilterEmpty(Filtre value)
{
    return value.Terme.IsNullOrEmpty() && value.Type == null && value.Km == null && value.MyHike == null;
}
```
---
# Location
```csharp
public async Task<List<string>> GetLocationAsync(string codePostal)
{
    if (codePostal == null){
        throw new ArgumentNullException(nameof(codePostal), "InexistentPostalCode");
    }

    List<string> result = new List<string>();

    try{
        string url = $"http://api.geonames.org/postalCodeSearch?postalcode={codePostal}&maxRows=10&username=hoilun2167686";

        using (HttpClient httpClient = new HttpClient())
        {
            using (Stream stream = await httpClient.GetStreamAsync(url))
            {
                XElement xml = await XElement.LoadAsync(stream, LoadOptions.None, CancellationToken.None).ConfigureAwait(false);

                var locations = xml.Descendants("code").Select(g => new
                {
                    Lat = g.Element("lat")?.Value,
                    Long = g.Element("lng")?.Value
                }).ToList();

                foreach (var point in locations.Where(point => point.Lat != null && point.Long != null))
                {
                    result.Add(point.Lat);
                    result.Add(point.Long);
                }
            }
        }

        if (result.Count == 0)
        {
            throw new Exception("NoLocationMatching");
        }
    }
    catch (HttpRequestException ex)
    {
        throw new Exception("Failed to retrieve location information.", ex);
    }
    catch (Exception ex)
    {
        throw new Exception("LocationReadingError", ex);
    }

    return result;
}
```


```csharp
public async Task<List<string>> GetLocationAsync(string codePostal)
{
    if (codePostal == null)
    {
        throw new ArgumentNullException(nameof(codePostal), "InexistentPostalCode");
    }

    List<string> result = new List<string>();

    try
    {
        string url = $"http://api.geonames.org/postalCodeSearch?postalcode={codePostal}&maxRows=10&username=hoilun2167686";

        using (HttpClient httpClient = new HttpClient())
        {
            using (Stream stream = await httpClient.GetStreamAsync(url).ConfigureAwait(false))
            {
                XElement xml = await XElement.LoadAsync(stream, LoadOptions.None, CancellationToken.None).ConfigureAwait(false);

                var locationTasks = xml.Descendants("code").AsParallel().Select(async g =>
                {
                    var lat = g.Element("lat")?.Value;
                    var lng = g.Element("lng")?.Value;

                    if (lat != null && lng != null)
                    {
                        await Task.Run(() =>
                        {
                            lock (result)
                            {
                                result.Add(lat);
                                result.Add(lng);
                            }
                        }).ConfigureAwait(false);
                    }
                });

                await Task.WhenAll(locationTasks).ConfigureAwait(false);
            }
        }

        if (result.Count == 0)
        {
            throw new Exception("NoLocationMatching");
        }
    }
    catch (HttpRequestException ex)
    {
        throw new Exception("Failed to retrieve location information.", ex);
    }
    catch (Exception ex)
    {
        throw new Exception("LocationReadingError", ex);
    }

    return result;
}
```
--- 
# Postal Code

```csharp
public string? ValidatePostalCode(string postalCode, string country)
{
    if (string.IsNullOrWhiteSpace(postalCode))
    {
        throw new ArgumentNullException(nameof(postalCode), "InexistentPostalCode");
    }

    postalCode = postalCode.Trim();
    var us = new Regex("^\\d{5}(-{0,1}\\d{4})?$");
    // var usRegex = new Regex(@"^\d{5}(-{0,1}\d{4})?$");
    var ca = new Regex("^[ABCEGHJ-NPRSTVXY]{1}[0-9]{1}[ABCEGHJ-NPRSTV-Z]{1}[ ]?[0-9]{1}[ABCEGHJ-NPRSTV-Z]{1}[0-9]{1}$");
// Regex(@"^[ABCEGHJ-NPRSTVXY]{1}\d{1}[ABCEGHJ-NPRSTV-Z]{1}[ ]?\d{1}[ABCEGHJ-NPRSTV-Z]{1}\d{1}$");

    // if ((type == "us" && us.IsMatch(postalCode)) || (type == "ca" && ca.IsMatch(postalCode)))
    if (type == "ca" && ca.IsMatch(postalCode))
    {
        return postalCode;
    }

    return null;
}
```

---
# Notification
``` dart
import 'dart:ui';  
  
import 'package:awesome_notifications/awesome_notifications.dart';  
import 'package:flutter/material.dart';  
  
class NotificationService {  
static Future<void> initializeNotification() async {  
await AwesomeNotifications().initialize(  
null,  
[  
NotificationChannel(  
groupKey: 'high_importance_channel',  
channelKey: 'high_importance_channel',  
channelName: 'Basic notification',  
channelDescription: 'Notification channel for basic test',  
defaultColor: const Color(0xFF9D50DD),  
ledColor: Colors.white,  
importance: NotificationImportance.Max,  
channelShowBadge: true,  
onlyAlertOnce: true,  
playSound: true,  
criticalAlerts: true,  
)  
],  
channelGroups: [  
NotificationChannelGroup(  
channelGroupKey: 'high_importance_channel_group',  
channelGroupName: 'Group 1',  
)  
],  
debug: true,  
);  
await AwesomeNotifications()  
.isNotificationAllowed()  
.then((isAllowed) async {  
if (!isAllowed){  
await AwesomeNotifications().requestPermissionToSendNotifications();  
}  
});  
await AwesomeNotifications().setListeners(  
onActionReceivedMethod: onActionReceivedMethod,  
onNotificationCreatedMethod: onNotificationCreatedMethod,  
onNotificationDisplayedMethod: onNotificationDisplayedMethod,  
onDismissActionReceivedMethod: onDismissActionReceivedMethod,  
);  
}  
  
static Future<void> onNotificationCreatedMethod(  
ReceivedNotification receivedNotification) async {  
debugPrint('onNotificationCreatedMethod');  
}  
static Future<void> onNotificationDisplayedMethod(  
ReceivedNotification receivedNotification) async {  
debugPrint('onNotificationDisplayedMethod');  
}  
static Future<void> onDismissActionReceivedMethod(  
ReceivedNotification receivedAction) async {  
debugPrint('onDismissActionReceivedMethod');  
}  
static Future<void> onActionReceivedMethod(  
ReceivedNotification receivedAction) async {  
debugPrint('onActionReceivedMethod');  
final payload = receivedAction.payload ?? {};  
if (payload["nagivate"] == true) {  
  
// MaterialPageRoute(builder: (_)=>const );  
}  
}  
static Future<void> showNotification({  
required final String title,  
required final String body,  
final String? summary,  
final Map<String, String>? paylod,  
final ActionType actionType = ActionType.Default,  
final NotificationLayout notificationLayout = NotificationLayout.Default,  
final NotificationCategory? category,  
final String? bigPicture,  
final List<NotificationActionButton>? actionButtons,  
final bool scheduled = false,  
final int? interval,  
}) async {  
assert(!scheduled || (scheduled && interval != null));  
  
await AwesomeNotifications().createNotification(  
content: NotificationContent(  
id: -1,  
channelKey: 'high_importance_channel',  
title: title,  
body: body,  
actionType: actionType,  
notificationLayout: notificationLayout,  
summary: summary,  
category: category,  
payload: paylod,  
bigPicture: bigPicture,  
),  
actionButtons: actionButtons,  
schedule: scheduled  
? NotificationInterval(  
interval: interval,  
timeZone: await AwesomeNotifications().getLocalTimeZoneIdentifier(),  
preciseAlarm: true,  
): null,  
);  
}  
}

```