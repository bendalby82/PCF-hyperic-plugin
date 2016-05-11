# PCF Hyperic plugin Build

1. Clone this repository and `cd` into it and execute:
```
  mvn package
```
  The jar will build in target [local git repo]/target/PCF-plugin.jar
  
  Specific tests in this build connect to the Ops Metric server, VPN is required.

# PCF Hyperic plugin Install

This plugin will auto discover PCF metric endpoints via the Pivotal Ops Metrics tile and allow uplink into Hyperic for collection.

Simplified Setup Instructions:

1. Install Hyperic Agent on Linux VM/Host

2. Register Hyperic Agent with the Hyperic Server

3. Copy the plugin jar ( PCF-plugin.jar ) to the following locations:

    server:`[hyperic root]/hq-engine/hq-server/webapps/ROOT/WEB-INF/hq-plugins`

    client:`[hyperic agent root]/agent-#.#.#-EE/bundles/agent-x86-64-linux-#.#.#/pdk/plugins`

4. Restart the Hyperic Agent

5. Navigate to the Hyperic Dashboard & Accept the Discovery of the "Pivotal CloudFoundry 1.4.x" Server Object associated with the Linux Agent Platfrom.


Additional KPIs can be added by editing the etc/hq-plugin.xml, and ensuring the query strings match according to the JMX properties exposed by Ops Metrics.

* Currently Pivotal Ops Metrics does not support external connections of DNAT from the Hyperic Agent.

# PCF Hyperic plugin and SSL

If you have secured the Pivotal Ops Metrics JMX endpoint with SSL, you will need to take the following steps to allow the plugin to connect:

1. Patch the JRE that is bundled with Hyperic Agent to 1.8.x or better (this is because Pivotal Ops Metrics uses TLS v1.2, whereas the bundled 1.7 JRE tries to use TLS v1.1):  
```  
    (On Windows)  
    cd c:\YOUR\INSTALL\FOLDER\hyperic-hqee-agent-5.8.4\bundles\agent-x86-64-win-5.8.4  
    move jre jre-1-7  
    xcopy /E "c:\Program Files\Java\jre1.8.0_73" jre   
```  
2. Create a trust store for the Ops Metrics server certificate on the machine running Hyperic Agent:  
```  
   (On Windows)  
   keytool -import -file %HOMEPATH%\documents\pcf-ops-metrics-ssl-cert.txt -alias pcf-ops-metrics -keystore c:\YOUR\PATH\pcfTrustStore.truststore
```  
3. Pass in the trust store to the Service Wrapper so that the JVM running the Hyperic Agent starts with the Pivotal Ops Metrics certificate loaded:  
```  
    (On Windows)  
    Add following lines to c:\YOUR\INSTALL\FOLDER\hyperic-hqee-agent-5.8.4\bundles\agent-x86-64-win-5.8.4\conf\wrapper.conf  
    wrapper.java.additional.7=-Djavax.net.ssl.trustStore=c:/YOUR/PATH/pcfTrustStore.truststore  
    wrapper.java.additional.8=-Djavax.net.ssl.trustStorePassword=XXX  
```  
 
