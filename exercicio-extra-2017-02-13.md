# Apache HTTP Server -------------------------------------------------------------------------------------------------------------------

                                                  -*- coding: utf-8 -*-

Changes with Apache 2.4.25

  *) Fix some build issues related to various modules.
     [Rainer Jung]

Changes with Apache 2.4.24

  *) SECURITY: CVE-2016-8740 (cve.mitre.org)
     mod_http2: Mitigate DoS memory exhaustion via endless
     CONTINUATION frames.
     [Naveen Tiwari <naveen.tiwari@asu.edu> and CDF/SEFCOM at Arizona State
     University, Stefan Eissing]

  *) SECURITY: CVE-2016-5387 (cve.mitre.org)
     core: Mitigate [f]cgi "httpoxy" issues.
     [Dominic Scheirlinck <dominic vendhq.com>, Yann Ylavic]

  *) SECURITY: CVE-2016-2161 (cve.mitre.org)
     mod_auth_digest: Prevent segfaults during client entry allocation when
     the shared memory space is exhausted.
     [Maksim Malyutin <m.malyutin dsec.ru>, Eric Covener, Jacob Champion]

  *) SECURITY: CVE-2016-0736 (cve.mitre.org)
     mod_session_crypto: Authenticate the session data/cookie with a
     MAC (SipHash) to prevent deciphering or tampering with a padding
     oracle attack.  [Yann Ylavic, Colm MacCarthaigh]

  *) SECURITY: CVE-2016-8743 (cve.mitre.org)
     Enforce HTTP request grammar corresponding to RFC7230 for request lines
     and request headers, to prevent response splitting and cache pollution by
     malicious clients or downstream proxies. [William Rowe, Stefan Fritsch]

  *) Validate HTTP response header grammar defined by RFC7230, resulting
     in a 500 error in the event that invalid response header contents are
     detected when serving the response, to avoid response splitting and cache
     pollution by malicious clients, upstream servers or faulty modules.
     [Stefan Fritsch, Eric Covener, Yann Ylavic]

  *) mod_rewrite: Limit runaway memory use by short circuiting some kinds of
     looping RewriteRules when the local path significantly exceeds 
     LimitRequestLine.  PR 60478. [Jeff Wheelhouse <apache wheelhouse.org>]

  *) mod_ratelimit: Allow for initial "burst" amount at full speed before
     throttling: PR 60145 [Andy Valencia <ajv-etradanalhos vsta.org>,
     Jim Jagielski]

  *) mod_socache_memcache: Provide memcache stats to mod_status.
     [Jim Jagielski]

  *) http_filters: Fix potential looping in new check_headers() due to new
     pattern of ap_die() from http header filter. Explicitly clear the
     previous headers and body.

  *) core: Drop Content-Length header and message-body from HTTP 204 responses.
     PR 51350 [Luca Toscano]

  *) mod_proxy: Honor a server scoped ProxyPass exception when ProxyPass is
     configured in <Location>, like in 2.2. PR 60458.
     [Eric Covener]

  *) mod_lua: Fix default value of LuaInherit directive. It should be 
     'parent-first' instead of 'none', as per documentation.  PR 60419
     [Christophe Jaillet]

  *) core: New directive HttpProtocolOptions to control httpd enforcement
     of various RFC7230 requirements. [Stefan Fritsch, William Rowe]

  *) core: Permit unencoded ';' characters to appear in proxy requests and
     Location: response headers. Corresponds to modern browser behavior.
     [William Rowe]

  *) core: ap_rgetline_core now pulls from r->proto_input_filters.

  *) core: Correctly parse an IPv6 literal host specification in an absolute
     URL in the request line. [Stefan Fritsch]

  *) core: New directive RegisterHttpMethod for registering non-standard
     HTTP methods. [Stefan Fritsch]

  *) mod_socache_memcache: Pass expiration time through to memcached.
     [Faidon Liambotis <paravoid debian.org>, Joe Orton]

  *) mod_cache: Use the actual URI path and query-string for identifying the
     cached entity (key), such that rewrites are taken into account when
     running afterwards (CacheQuickHandler off).  PR 21935.  [Yann Ylavic]

  *) mod_http2: new directive 'H2EarlyHints' to enable sending of HTTP status
     103 interim responses. Disabled by default. [Stefan Eissing]
     
  *) mod_ssl: Fix quick renegotiation (OptRenegotiaton) with no intermediate
     in the client certificate chain.  PR 55786.  [Yann Ylavic]

  *) event: Allow to use the whole allocated scoreboard (up to ServerLimit
     slots) to avoid scoreboard full errors when some processes are finishing
     gracefully. Also, make gracefully finishing processes close all
     keep-alive connections. PR 53555. [Stefan Fritsch]

  *) mpm_event: Don't take over scoreboard slots from gracefully finishing
     threads. [Stefan Fritsch]

  *) mpm_event: Free memory earlier when shutting down processes.
     [Stefan Fritsch]

  *) mod_status: Display the process slot number in the async connection
     overview. [Stefan Fritsch]

  *) mod_dir: Responses that go through "FallbackResource" might appear to
     hang due to unterminated chunked encoding. PR58292. [Eric Covener]

  *) mod_dav: Fix a potential cause of unbounded memory usage or incorrect
     behavior in a routine that sends <DAV:response>'s to the output filters.
     [Evgeny Kotkov]

  *) mod_http2: new directive 'H2PushResource' to enable early pushes before 
     processing of the main request starts. Resources are announced to the 
     client in Link headers on a 103 early hint response. 
     All responses with status code <400 are inspected for Link header and
     trigger pushes accordingly. 304 still does prevent pushes.
     'H2PushResource' can mark resources as 'critical' which gives them higher
     priority than the main resource. This leads to preferred scheduling for
     processing and, when content is available, will send it first. 'critical'
     is also recognized on Link headers. [Stefan Eissing]
     
  *) mod_proxy_http2: uris in Link headers are now mapped back to a suitable
     local url when available. Relative uris with an absolute path are mapped
     as well. This makes reverse proxy mapping available for resources
     announced in this header. 
     With 103 interim responses being forwarded to the main client connection,
     this effectively allows early pushing of resources by a reverse proxied
     backend server. [Stefan Eissing]
     
  *) mod_proxy_http2: adding support for newly proposed 103 status code.
     [Stefan Eissing]
     
  *) mpm_unix: Apache fails to start if previously crashed then restarted with
     the same PID (e.g. in container).  PR 60261.
     [Val <valentin.bremond gmail.com>, Yann Ylavic]

  *) mod_http2: unannounced and multiple interim responses (status code < 200)
     are parsed and forwarded to client until a final response arrives.
     [Stefan Eissing]
  
  *) mod_proxy_http2: improved robustness when main connection is closed early
     by resetting all ongoing streams against the backend.
     [Stefan Eissing]
  
  *) mod_http2: allocators from slave connections are released earlier,
     resulting in less overall memory use on busy, long lived connections.
     [Stefan Eissing]
     
  *) mod_remoteip: Pick up where we left off during a subrequest rather
     than running with the modified XFF but original TCP address.
     PR 49839/PR 60251

  *) http: Respond with "408 Request Timeout" when a timeout occurs while
     reading the request body.  [Yann Ylavic]

  *) mod_http2: connection shutdown revisited: corrected edge cases on
     shutting down ongoing streams, changed log warnings to be less noisy
     when waiting on long running tasks. [Stefan Eissing]

  *) mod_http2: changed all AP_DEBUG_ASSERT to ap_assert to have them 
     available also in normal deployments. [Stefan Eissing]

  *) mod_http2/mod_proxy_http2: 100-continue handling now properly implemented
     up to the backend. Reused HTTP/2 proxy connections with more than a second
     not used will block request bodies until a PING answer is received.
     Requests headers are not delayed by this, since they are repeatable in
     case of failure. This greatly increases robustness, especially with
     busy server and/or low keepalive connections. [Stefan Eissing]
     
  *) mod_proxy_http2: fixed duplicate symbols with mod_http2.
     [Stefan Eissing]
  
  *) mod_http2: rewrite of how responses and trailers are transferred between
     master and slave connection. Reduction of internal states for tasks
     and streams, stability. Heuristic id generation for slave connections
     to better keep promise of connection ids unique at given point int time.
     Fix for mod_cgid interop in high load situtations. 
     Fix for handling of incoming trailers when no request body is sent.
     [Stefan Eissing]
  
  *) mod_http2: fix suspended handling for streams. Output could become
     blocked in rare cases. [Stefan Eissing]

  *) mpm_winnt: Prevent a denial of service when the 'data' AcceptFilter is in
     use by replacing it with the 'connect' filter. PR 59970. [Jacob Champion]

  *) mod_cgid: Resolve a case where a short CGI response causes a subsequent
     CGI to be killed prematurely, resulting in a truncated subsequent
     response. [Eric Covener]

  *) mod_proxy_hcheck: Set health check URI and expression correctly for health
     check worker. PR 60038 [zdeno <zdeno@scnet.sk>]

  *) mod_http2: if configured with nghttp2 1.14.0 and onward, invalid request
     headers will immediately reset the stream with a PROTOCOL error. Feature
     logged by module on startup as 'INVHD' in info message.
     [Stefan Eissing]
     
  *) mod_http2: fixed handling of stream buffers during shutdown.
     [Stefan Eissing]
     
  *) mod_reqtimeout: Fix body timeout disabling for CONNECT requests to avoid
     triggering mod_proxy_connect's AH01018 once the tunnel is established.
     [Yann Ylavic]

  *) ab: Set the Server Name Indication (SNI) extension on outgoing TLS
     connections (unless -I is specified), according to the Host header (if
     any) or the requested URL's hostname otherwise.  [Yann Ylavic]

  *) mod_proxy_fcgi: avoid loops when ProxyErrorOverride is enabled
     and the error documents are proxied. PR 55415. [Luca Toscano]

  *) mod_proxy_fcgi: read the whole FCGI response even when the content
     has not been modified (HTTP 304) or in case of a precondition failure
     (HTTP 412) to avoid subsequent bogus reads and confusing
     error messages logged. [Luca Toscano]

  *) mod_http2: h2 status resource follows latest draft, see
     http://www.ietf.org/id/draft-benfield-http2-debug-state-01.txt
     [Stefan Eissing]
     
  *) mod_http2: handling graceful shutdown gracefully, e.g. handling existing
     streams to the end. [Stefan Eissing]
  
  *) mod_proxy_{http,ajp,fcgi}: don't reuse backend connections with data
     available before the request is sent.  PR 57832.  [Yann Ylavic]

  *) mod_proxy_balancer: Prevent redirect loops between workers within a
     balancer by limiting the number of redirects to the number balancer
     members. PR 59864 [Ruediger Pluem]

  *) mod_proxy: Correctly consider error response codes by the backend when
     processing failonstatus. PR 59869 [Ruediger Pluem]

  *) mod_dav: Add dav_get_provider_name() function to obtain the name
     of the provider from mod_dav.  [Graham Leggett]

  *) mod_dav: Add support for childtags to dav_error.
     [Jari Urpalainen <jari.urpalainen nokia.com>]

  *) mod_proxy_fcgi: Fix 2.4.23 breakage for mod_rewrite per-dir and query 
     string showing up in SCRIPT_FILENAME. PR59815

  *) mod_include: Fix a potential memory misuse while evaluating expressions.
     PR59844. [Eric Covener]

  *) mod_http2: new H2CopyFiles directive that changes treatment of file
     handles in responses. Necessary in order to fix broken lifetime handling
     in modules such as mod_wsgi.
  
  *) mod_http2: removing timeouts on master connection while requests are
     being processed. Requests may timeout, but the master only times out when
     no more requests are active. [Stefan Eissing]
     
  *) mod_http2: fixes connection flush when answering SETTINGS without any
     stream open. [Moto Ishizawa <@summerwind>, Stefan Eissing]
     


  [Apache 2.3.0-dev includes those bug fixes and changes with the
   Apache 2.2.xx tree as documented, and except as noted, below.]

Changes with Apache 2.2.x and later:

  *) http://svn.apache.org/viewvc/httpd/httpd/branches/2.2.x/CHANGES?view=markup

Changes with Apache 2.0.x and later:

  *) http://svn.apache.org/viewvc/httpd/httpd/branches/2.0.x/CHANGES?view=markup
  
  
  # Tomcat -------------------------------------------------------------------------------------------------------------------
  
  Cluster
add	Make the accessTimeout configurable in BackupManager. The accessTimeout is used as a timeout period for PING in replication map. (kfujino)
Web applications
fix	Ensure the ASF logo image is correctly displayed in docs and host-manager applications. (violetagg)

  
  # Eclpse -------------------------------------------------------------------------------------------------------------------
  
  Eclipse Project Release Notes
Target Operating Environments
Compatibility with Previous Releases
Compatibility of Release 4.6 with 4.5
Known Issues
General problems
General - Startup
Installation/Configuration issues that can cause Eclipse to fail start
Invalid characters in install directory prevents Eclipse from starting
Hanging during class loading when out of permanent generation memory
General - GCJ
Problems with classloaders in created threads
Deadlock creating executable extension in Plugin.startup
Potential Problems Converting Plug-in Manifests
Location for Debug Options File on Mac OS
Issues with JNI that use FindClass
Platform - Ant
Custom Ant tasks and Ant types must be separate from plug-in library JARs
XDoclet support from within Eclipse
Ant Editor code completion based on Ant 1.6.x
Setting build loggers not supported when debugging Ant builds
Renaming an External Tool builder set to run during auto-build will cause errors
Slow typing/saving of the Ant editor with imports that define numerous macrodefs
Ant 1.8.x reports missing libraries as build failures
Platform - User Assistance
Welcome page not displayed properly (Linux/Unix)
Help browser tool bar buttons do not work for some documents
Help documents not displayed in a browser or very slow document loading (Windows only)
Working disconnected from the network (Windows only)
Using Internet Explorer in offline mode (Windows only)
Platform - UI
Dirty state not tracked properly for OLE documents (Windows only)
OLE document crashes can cause Eclipse to also crash (Windows only)
Toolbars only containing contributed controls exhibit display errors on Mac/Linux
Allocating enough memory and solving OutOfMemoryError
Capabilities and Activities don't affect the menus and toolbars
Platform - SWT
Non-uniform scaling of text vs. icons when using intermediate scaling factors on high-DPI displays
Eclipse plug-in based on the SWT Browser throws exception
SWT Browser widget does not work on some recent Linux versions e.g Ubuntu 16.04
SWT Browser widget crashes when run with XULRunner 31 run-time (Linux only)
IME conversion problem (Solaris GTK only)
Eclipse won't start (Linux GTK PPC only)
Eclipse icon is duplicated in task-bar on Windows
BIDI Segments in Text controls
Block Selection functionality provided by StyledText is not BIDI aware
Platform - Team - CVS
CVS server compatibility
Connection cannot be found after initially missing
Received broken pipe signal" error from server
Terminated with fatal signal 10" error from server
error using ext connection method
A disabled CVS capability may not be auto-enabled in existing workspaces
Builder output files may appear as changed
Enabling GNOME proxy support
Platform - Install/Update
Manually installing features and plug-ins on a FAT file system (Windows only)
Connecting to untrusted sites using https
Extension location is lost if the install path changes
Java development tools (JDT)
Multiple regions formatting in a given source snippet
Searching for constant field references
Cut, copy, paste not working for linked resources in views showing Java elements
Java working sets not working correctly for elements from JRE system library container
Breakpoints unreliable running Sun 1.6.0_14
Suspend on uncaught exception overrides exception breakpoint location filters
Running Java programs with non-Latin-1 characters in package or class names
Cannot run or debug class in a project with GB18030 characters in project name
Cannot detect installed JRE with GB18030 characters in path name
Cannot generate Javadoc for packages with GB18030 characters in the name
Unable to debug stack overflows
Evaluation limitation
Missing debug attributes
Using Hot Code Replace
Scrapbook
Debugging over slow connections
Updating of inspected values
Stepping over native methods that perform I/O
VM and process termination running on IBM 1.3 JVM on Linux (Linux only)
Java Annotation Processing
Java indexing encounters problems when a folder is used both as a source and a class folder
Plug-in Development Environment (PDE)
Feature manifest editor does not preserve all comments
PDE will not unzip source zips of some plug-ins
Emacs key bindings do not work in manifest editor fields
Export of plug-in may silently drop classes
Compilation errors when exporting projects not stored outside of the workspace
Headless build needs to be run from a fully qualified path
Importing in Eclipse application fails if plug-in exists in host workspace
Reusing a workspace after changing architectures silently breaks PDE models
Missing @since tag API Tools problems on interface fields containing @noreference tag
Running Eclipse
Allocating enough memory and solving OutOfMemoryErrors
Selecting a workspace
Specifying the Java virtual machine
Mac OS X
Shared Install
Special Operating System Requirements
AIX
HPUX
Solaris
Upgrading Workspace from a Previous Release
Users who don't use "-data"
Users who do use "-data"
Users who use User Libraries or classpath containers that contain JARs referencing other libraries via Class-Path in the MANIFEST.MF
Dropped in bundles may not resolve after upgrade
Interoperability with Previous Releases
Interoperability of Release 4.6 with previous releases
Sharing projects between heterogeneous Eclipse 4.6 and 4.5
Using Eclipse 4.6 to develop plug-ins that work in Eclipse 4.5
Appendix: Execution Environment by Bundle
