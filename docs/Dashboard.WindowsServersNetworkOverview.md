# Windows Servers Network Overview
[Screenshot](../pics/DashboardWindowsServersNetworkOverview.png)  
This dashboard shows the network bandwidth used by the Windows servers in a Prometheus data source. Any number of servers can be selected, and all the graphs display on one page.

- The servers can be selected using the drop down at the top of the page
- A soft max of 1 MiB is used to keep the Y axis from making small fluctuations of nearly idle servers appear more significant than they are. Without a soft max, the Y axis automatically adjusts to the data, so no matter how little the data flucutates it will always take up the whole graph.

The key to making this dashboard work is to create a single panel displaying the desired information for a single server, and then using the `Panel options > Repeat options` to repeat it for each Windows server selected.