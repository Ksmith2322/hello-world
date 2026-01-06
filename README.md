timeseries dp_cpu = avg(paloalto.dataplane.cpu.utilization),
  by:{sys.name, dataplane}
| sort arrayAvg(dp_cpu) desc
