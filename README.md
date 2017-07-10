# Java Flame Graphs on Gentoo Linux
This is intentionally a very "hands on" manual process in order to gain familiarity with the underlying tooling and related dependencies.

Source code is being downloaded to `/projects/perf/`.  Change as needed.

### Java "perf-map-agent"
* Fastest Method to produce Java flame graphs
* Built in flame graph support `perf-java-flames <pid> <perf-record-options>`  
* Requires Java 8u60 and above.

Follow the [install instructions](https://github.com/jvm-profiling-tools/perf-map-agent).  
Clone the source code for generating flame graphs:  
```
cd /projects/perf/
git clone https://github.com/brendangregg/FlameGraph
export FLAMEGRAPH_DIR=/projects/perf/FlameGraph
cd /projects/perf/perf-map-agent/bin/
./perf-java-flames <JAVA_PID>
```  
Add the following VM arguments to the profiled application:
* -XX:+PreserveFramePointer
  * Required
* -XX:+UnlockDiagnosticVMOptions
  * Optional
* -XX:+DebugNonSafepoints
  * Optional
  * Unfolded frames will be more accurate

### Generating Graphs Using Performance Co-Pilot and Vector
PCP Dependencies: 
* net-libs/libmicrohttpd  
[Install PCP](http://vectoross.io/docs/installing-performance-co-pilot)  

After installation, start the services:
```
/etc/init.d/pmcd start
/etc/init.d/pmwebd start
```

PCP looks like it has some interesting metrics by itself
[PCP Quick Reference](http://pcp.io/docs/guide.html)

[Install Vector](http://vectoross.io/docs/)
There are different Methods.  Using **"Deploy to a Web Server".**  
OS: Gentoo  
Web Server: Apache2  
` cd /var/www/localhost/htdocs`  
Follow [Instructions](http://vectoross.io/docs/deploying-web-server.html)
`/etc/init.d/apache2 start`  
Then from web browser `localhost:80`  

**Vector doesn't support flame graphs by default.**  
Start with [Vector Flame Graphs](http://vectoross.io/docs/cpu-flame-graphs.html)
```
sudo -s
export $PCP_PMDAS_DIR=/var/lib/pcp/pmdas
cd $PCP_PMDAS_DIR
git clone https://github.com/spiermar/generic-pmda
cd generic-pmda
./Install
```

It says once PMDA is working set `enableCpuFlameGraph` to `true` but it looks like this means you have to build it from source now?!
because `app/index.config.js` is in the [src](https://github.com/Netflix/vector/tree/master/src/app) directory.  Bloody hell...

See [Building Vector from Source](http://vectoross.io/docs/building-vector-source.html)

Install bower `sudo npm install -g bower`

Install gulp `sudo npm install -g gulp`

Verify that dependencies are installed `npm list -g --depth=0`
> /usr/lib  
> ├── bower@1.8.0  
> ├── gulp@3.9.1  
> └── npm@4.2.0  

Perf (and the flame graph pdma) need additional java specific tools.  See `perf-map-agent` above.

#### Additional Reading
[Intro to Flame Graphs](http://brendangregg.com/flamegraphs.html)  
[Latest Java Info (2016)](http://www.brendangregg.com/FlameGraphs/cpuflamegraphs.html#Java)  
[Named Thread Flamegraphs](http://epickrram.blogspot.com/2017/03/named-thread-flamegraphs.html)  

#### Packaged Sources
[Both PCP and Vector](https://dl.bintray.com/pcp/source/)

#### Utils
`sys-process/tiptop` shows IPC (Instructions Per Clock).  
Want this as closed to 1.0 as possible. See [CPU Utilization is Wrong](http://brendangregg.com/blog/2017-05-09/cpu-utilization-is-wrong.html)
