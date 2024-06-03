# Instructions on Building the "Windows Servers Overview" Dashboard
## Create variables
We need to query for all the Windows servers in Prometheus. To do that, we create a variable with all the jobs that have the `windows_cs_hostname` metric.

Under the variable tab of the dashboard's settings, create a new variable.
```
Variable type: Query  
Name: job  
Show on dashboard: Nothing
Query type: Label values
Label: job
Metric: windows_cs_hostname
Refresh: On dashboard load
```

## Add Visualization
Visualization type: Table  
Title: Overview  

### Create queries
Note that although I'm using `max` for a lot of the queries below, since they're instant queries I could use almost any similar function. I just needed something so I could group by hostname.

#### Query 1
```
Name: OS Version  
Query: max (windows_os_info{job="$job"}) by (hostname,product,version,revision)  
Format: Table  
Type: Instant  
```

#### Query 2
```
Name: Status  
Query: max (up{job="$job"}) by (hostname)  
Format: Table  
Type: Instant  
```

#### Query 3
```
Name: Uptime  
Query: max (time()-windows_system_system_up_time{job="$job"}) by (hostname)  
Format: Table  
Type: Instant  
```

#### Query 4
```
Name: Memory Usage  
Query: max (100.0-100 * windows_os_physical_memory_free_bytes{job="$job"} / windows_cs_physical_memory_bytes{job="$job"}) by (hostname)  
Format: Table  
Type: Instant  
```

#### Query 5
```
Name: Max Disk Usage  
Query: sort (max (100 - 100 * windows_logical_disk_free_bytes{job="$job",volume=~"(^.:$)"}/windows_logical_disk_size_bytes{job="$job",volume=~"(^.:$)"}) by (hostname,volume))  
Format: Table  
Type: Instant  
```

## Create data transformations
Join all queries by hostname to create each row
```
1 - Join by field
Mode: Outer (Time Series)
Field: hostname
```
Filter to just the fields we want to display
```
2 - Filter fields by name
Select: hostname, product, revision, Value #Status, Value #Uptime, Value #Memory Usage, volume, Value #Max Disk Usage
```

Rename some of the fields to more friendly names
```
3 - Organize fields by name
product: Operating System
Value #STatus: Status
Value #Uptime: Uptime
Value #Memory Usage: % RAM Used
volume: Fullest volume
Value #Max Disk Usage: % Disk Used
```
Sort by hostname and then status
```
4 - Sort by
Field: hostname

5 - Sort by
Field: Status
```

## Create overrides
Configure the Status field so it shows Up for 1 and Down for 0, and color them green or red respectively
```
Fields with name: Status
- Value mappings
  - 1: Up
  - 2: Down
- Thresholds
  - Green: 1
  - Red: 0
  - Mode: Absolute
- Cell options > Cell type: Colored text
```
Set the base color of the Uptime field to yellow, and have it display green if it's over 172800 seconds (2 days)
```
Fields with name: Uptime
- Thresholds
  - Green: 172800
  - Yellow: Base
- Standard Options > Unit: duration(d hh:mm:ss)
- Cell optoins > Cell type: Colored text
```
Set `%RAM Used` and `% Disk Used` columns to use colored text so they'll display the default thresholds of red for > 80% and yellow for > 70% and < 80%.
```
Fields with name: % RAM Used
Cell options > Cell type: Colored text

Fields with name: % Disk Used
Cell options > Cell type: Colored text
```
Strip out "Microsoft Windows" from the Operating System name so it's shorter
```
Fields with name: Operating System
Value mappings: ^Microsoft Windows(.*) > $1
```
Convert the `hostname` field to a hyperlink to a dashboard with more detailed metrics for that server
```
Fields with name: hostname
- Data link
  - Title: $hostname
  - URL: /grafana/d/edgz29vfxxdkwe/windows-server?orgId=1&var-hostname=${__data.fields.hostname}
  - Open in new tab
```

