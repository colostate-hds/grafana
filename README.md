# Dashboards 
[Windows Servers Overview](docs/Dashboard.WindowsServersOverview.md)  
[Windows Server](docs/Dashboard.WindowsServer.md)  
[Windows Servers Network Overview](docs/Dashboard.WindowsServersNetworkOverview.md)  

All Windows dashboards use [Prometheus](https://prometheus.io/) for their datasource, and collect metrics via [windows_exporter](https://github.com/prometheus-community/windows_exporter).

# Building a dashboard
[Instructions for creating the Windows Server Overview dashboard](docs/Dashboard.WindowsServersOverview.Detail.md)  
This is the most complicated dashboard of the three. If you understand how to build this dashboard, the other two are simple in comparison.

# General information
The [Official Grafana Documentation](https://grafana.com/docs/grafana/latest/) is of course a good place to start. Grafana also has a [test environment](https://play.grafana.org/) that has a lot of high quality [example dashboards](https://play.grafana.org/dashboards).

# Functions: rate vs irate
Most community dashboards use the `irate` function, but the problem with `irate` is that it always uses the last two data points of your interval. As you increase your interval, you miss increasingly large chunks of data. I suspect the reason so many use `irate` is because in the past there wasn't a great way to set your range interval for `rate` so that you were guaranteed to always have two data points. If you decreased your interval enough your charts would display a "No data" message. Since `irate` just grabs the last two data points, it would seem to work better. I'd personally rather get the "No data" message than have data that appears valid but is potentially missing information, but for whatever reason nearly every community dashboard I've looked at uses `irate`.

Grafana also added the `$_rate_interval` variable way back in 7.2, which intelligently picks an appropriate interval, so now there is even less of a reason to use `irate`.

## References

https://grafana.com/blog/2020/09/28/new-in-grafana-7.2-__rate_interval-for-prometheus-rate-queries-that-just-work/  
https://utcc.utoronto.ca/~cks/space/blog/sysadmin/PrometheusRateVsIrate  

