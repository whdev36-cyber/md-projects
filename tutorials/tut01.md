# Working with GPS in Python

GPS (Global Positioning System) functionality can be incorporated into Python applications for various purposes like tracking, navigation, and geospatial analysis. Here's a guide to working with GPS data in Python:

## Basic Approaches

### 1. Reading GPS Data from Serial Port (NMEA)

Most GPS modules output NMEA sentences via serial communication:

```python
import serial
import pynmea2

ser = serial.Serial('/dev/ttyUSB0', 9600, timeout=5.0)

while True:
    try:
        line = ser.readline().decode('ascii', errors='replace')
        if line.startswith('$GPRMC'):
            msg = pynmea2.parse(line)
            print(f"Latitude: {msg.latitude}, Longitude: {msg.longitude}")
            print(f"Speed: {msg.spd_over_grnd} knots")
    except serial.SerialException as e:
        print(f"Device error: {e}")
        break
    except pynmea2.ParseError as e:
        print(f"Parse error: {e}")
```

### 2. Using GPSD (GPS Daemon)

For Linux systems, you can interact with the GPSD service:

```python
import gpsd

# Connect to the local GPS daemon
gpsd.connect()

# Get GPS position
packet = gpsd.get_current()
print(f"Lat/Lon: {packet.position()}")
print(f"Altitude: {packet.altitude()}m")
print(f"Movement: {packet.movement()}")
```

## Popular Python GPS Libraries

1. **gpsd-py3**: Interface with GPSD daemon
2. **pynmea2**: Parse NMEA sentences
3. **geopy**: Geocoding and distance calculations
4. **gps3**: Python 3 compatible GPSD interface
5. **pygps**: Pure Python GPS toolkit

## Advanced GPS Applications

### Geofencing Example

```python
from geopy.distance import geodesic

def is_inside_geofence(current_pos, center_pos, radius_km):
    distance = geodesic(current_pos, center_pos).kilometers
    return distance <= radius_km

# Usage
home = (37.7749, -122.4194)  # San Francisco
current = (37.3382, -121.8863)  # San Jose
print(is_inside_geofence(current, home, 100))  # True
```

### Calculating Distance and Bearing

```python
from geopy import distance

point_a = (37.7749, -122.4194)  # San Francisco
point_b = (34.0522, -118.2437)  # Los Angeles

# Distance in km
dist = distance.distance(point_a, point_b).km
print(f"Distance: {dist:.2f} km")

# Bearing
initial_bearing = distance.great_circle(point_a, point_b).initial_bearing
print(f"Initial bearing: {initial_bearing:.2f}°")
```

## Mapping GPS Data

You can visualize GPS data using libraries like:

- **Folium** (interactive maps)
- **Matplotlib** (basic plotting)
- **Plotly** (interactive visualizations)

```python
import folium

# Create a map centered at the first GPS point
points = [(37.7749, -122.4194), (34.0522, -118.2437)]
m = folium.Map(location=points[0], zoom_start=6)

# Add markers for each point
for point in points:
    folium.Marker(point).add_to(m)

# Draw a line between points
folium.PolyLine(points, color="blue", weight=2.5, opacity=1).add_to(m)

# Save to HTML file
m.save('gps_track.html')
```

## Working with GPS Logs (GPX files)

```python
import gpxpy
import gpxpy.gpx

# Parse GPX file
with open('track.gpx', 'r') as gpx_file:
    gpx = gpxpy.parse(gpx_file)

for track in gpx.tracks:
    for segment in track.segments:
        for point in segment.points:
            print(f'Point at ({point.latitude},{point.longitude}) -> {point.elevation}')
```

## Tips for GPS Development

1. Always handle GPS data parsing with try-except blocks (data can be noisy)
2. Consider averaging multiple readings for better accuracy
3. Account for GPS signal loss scenarios
4. For time-sensitive applications, use proper timestamping
5. Consider magnetic vs true north differences in bearing calculations


