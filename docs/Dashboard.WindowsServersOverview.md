# Windows Servers Overview
[Screenshot](../pics/DashboardWindowsServersOverview.png)  
This dashboard provides a summary of all the Windows servers in a Prometheus data source. 

- It sorts by `Status` followed by `Hostname`, so servers that are down are at the top. This can be overridden by sorting by any column.
- `Revision` shows the latest OS update, which is useful as a quick check that the operating system is fully patched
- Thresholds are set to draw attention to potential problems (servers that have rebooted recently, servers that are low on RAM, or servers that have nearly full volumes)
- It shows the volume with the least disk space available (by percentage of total space) and how much space is being used
- The `Hostname` can be configured as a link to a dashboard showing more detailed metrics for that server (see notes below)

## Linking Server Specific Metrics using the Hostname field
1. Add a field override using the "Fields with name" option, and specify the `Hostname` field
2. Add an override property using the "Data Links" option
3. Add a link
   - Title: $hostname
   - URL: \<dashboard specific URL\>&var-hostname=${__data.fields.hostname}
     - Example: /grafana/d/edgz29vfxxdkwe/windows-server?orgId=1&var-hostname=${__data.fields.hostname}  

Note: You could [specify the dashboard UID](https://grafana.com/docs/grafana/latest/administration/provisioning/#reusable-dashboard-urls) in your JSON file to avoid having to manually configure this. However, if a dashboard already exists with that UID, it will be overwritten.

[Instructions on Building the Dashboard](Dashboard.WindowsServersOverview.Detail.md)
