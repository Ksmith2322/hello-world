timeseries sys_plane = avg('com.dynatrace.extension.palo-alto.generic.cpu.system.utilization'),
  by:{'dt.entity.palo-alto:device'}
| sort arrayAvg(sys_plane) desc
