
# NETCONF

NETCONF is defined in the [RFC 6241](https://tools.ietf.org/html/rfc6241)   

## Open a NetConf session inside an SSH connection

In order to open a NetConf session inside an SSH connection, there are two options defined in the RFC [(6242)](https://tools.ietf.org/html/rfc6242):   
- we can establish an SSH connection to a NetConf server, and then run the command netconf.  
- we can invoke the NetConf subsystem using the following command (the `-s` option causes the command netconf to be invoked):  `ssh device -s netconf -p 830`  

## NetConf server capabilities 

Once the NetConf session is open, the NetConf server advertises its capabilities.  
The NetConf server is the Junos device.   
In the below example, the NetConf server has the `candidate` capability, `confirmed-commit` capability, `validate` capability ...: They are defined in the RFC 6241.

```
$ ssh 100.123.1.0
Password:
Last login: Wed Nov 27 07:53:05 2019 from 100.123.35.0
--- JUNOS 18.2R1.9 Kernel 64-bit  JNPR-11.0-20180614.6c3f819_buil
jcluser@vMX-addr-0> netconf
<!-- No zombies were killed during the creation of this user interface -->
<!-- user jcluser, class j-super-user -->
<hello xmlns="urn:ietf:params:xml:ns:netconf:base:1.0">
  <capabilities>
    <capability>urn:ietf:params:netconf:base:1.0</capability>
    <capability>urn:ietf:params:netconf:capability:candidate:1.0</capability>
    <capability>urn:ietf:params:netconf:capability:confirmed-commit:1.0</capability>
    <capability>urn:ietf:params:netconf:capability:validate:1.0</capability>
    <capability>urn:ietf:params:netconf:capability:url:1.0?scheme=http,ftp,file</capability>
    <capability>urn:ietf:params:xml:ns:netconf:base:1.0</capability>
    <capability>urn:ietf:params:xml:ns:netconf:capability:candidate:1.0</capability>
    <capability>urn:ietf:params:xml:ns:netconf:capability:confirmed-commit:1.0</capability>
    <capability>urn:ietf:params:xml:ns:netconf:capability:validate:1.0</capability>
    <capability>urn:ietf:params:xml:ns:netconf:capability:url:1.0?protocol=http,ftp,file</capability>
    <capability>urn:ietf:params:xml:ns:yang:ietf-netconf-monitoring</capability>
    <capability>http://xml.juniper.net/netconf/junos/1.0</capability>
    <capability>http://xml.juniper.net/dmi/system/1.0</capability>
  </capabilities>
  <session-id>94347</session-id>
</hello>
]]>]]>
```

## Get interface ge-0/0/0 configuration

We can use the operation get-config with a subtree filter to check if the interface ge-0/0/0 is already configured in the running (i.e active) configuration.  
The interface ge-0/0/0 is already configured  

```
]]>]]>
<rpc>
    <get-config>
        <source>
            <running/>
        </source>
        <filter type="subtree">
            <configuration>
                <interfaces>
                    <interface>
                       <name>ge-0/0/0</name>
                    </interface>
                </interfaces>
            </configuration>
        </filter>
    </get-config>
</rpc>
]]>]]>
<rpc-reply xmlns="urn:ietf:params:xml:ns:netconf:base:1.0" xmlns:junos="http://xml.juniper.net/junos/18.2R1/junos">
<data>
<configuration xmlns="http://xml.juniper.net/xnm/1.1/xnm" junos:commit-seconds="1574828031" junos:commit-localtime="2019-11-27 04:13:51 UTC" junos:commit-user="jcluser">
    <interfaces>
        <interface>
            <name>ge-0/0/0</name>
            <description>to leaf vMX4</description>
            <unit>
                <name>0</name>
                <family>
                    <inet>
                        <address>
                            <name>192.168.1.0/31</name>
                        </address>
                    </inet>
                </family>
            </unit>
        </interface>
    </interfaces>
</configuration>
</data>
</rpc-reply>
]]>]]>

```

## Configure routing policies 

The routing policies configuration is not yet configured.  
We can use the operation get-config with a filter to retrieve the policy-options subtree from the active configuration:  
```
]]>]]>
<rpc>
    <get-config>
        <source><running/></source>
        <filter type="subtree">
            <configuration>
                <policy-options>
                </policy-options>
            </configuration>
        </filter>
    </get-config>
</rpc>
]]>]]>
<rpc-reply xmlns="urn:ietf:params:xml:ns:netconf:base:1.0" xmlns:junos="http://xml.juniper.net/junos/18.2R1/junos">
<data>
<configuration>
</configuration>
</data>
</rpc-reply>
]]>]]>

```
Let's use the lock operation in order to lock the candidate configuration:   
```
]]>]]>
<rpc><lock><target><candidate/></target></lock></rpc>]]>]]>
<rpc-reply xmlns="urn:ietf:params:xml:ns:netconf:base:1.0" xmlns:junos="http://xml.juniper.net/junos/18.2R1/junos">
<ok/>
</rpc-reply>
]]>]]>
```

Let's use the edit-config operation to load a configuration (Junos policy-options details) on the candidate configuration:  
```
]]>]]>
<rpc>
    <edit-config>
        <target>
            <candidate/>
        </target>
        <default-operation>merge</default-operation> 
        <config>
            <configuration>
                <policy-options>
                    <policy-statement>
                        <name>bgp-in</name>
                        <then>
                            <accept/>
                        </then>
                    </policy-statement>
                    <policy-statement>
                        <name>bgp-out</name>
                        <then>
                            <accept/>
                        </then>
                    </policy-statement>
                </policy-options>
            </configuration>
        </config>
    </edit-config>
</rpc>
]]>]]>
```

Let's use the operation get-config with a filter to retrieve the policy-options subtree from the candidate configuration: 
```
]]>]]>
<rpc>
    <get-config>
        <source><candidate/></source>
        <filter type="subtree">
            <configuration>
                <policy-options>
                </policy-options>
            </configuration>
        </filter>
    </get-config>
</rpc>
]]>]]>
<rpc-reply xmlns="urn:ietf:params:xml:ns:netconf:base:1.0" xmlns:junos="http://xml.juniper.net/junos/18.2R1/junos">
<data>
<configuration xmlns="http://xml.juniper.net/xnm/1.1/xnm" junos:changed-seconds="1574843342" junos:changed-localtime="2019-11-27 08:29:02 UTC">
    <policy-options>
        <policy-statement>
            <name>bgp-in</name>
            <then>
                <accept/>
            </then>
        </policy-statement>
        <policy-statement>
            <name>bgp-out</name>
            <then>
                <accept/>
            </then>
        </policy-statement>
    </policy-options>
</configuration>
</data>
</rpc-reply>
]]>]]>

```

Let's use the operation get-config with a filter to retrieve the policy-options subtree from the active configuration: 
```
]]>]]>
<rpc>
    <get-config>
        <source><running/></source>
        <filter type="subtree">
            <configuration>
                <policy-options>
                </policy-options>
            </configuration>
        </filter>
    </get-config>
</rpc>
]]>]]>
<rpc-reply xmlns="urn:ietf:params:xml:ns:netconf:base:1.0" xmlns:junos="http://xml.juniper.net/junos/18.2R1/junos">
<data>
<configuration>
</configuration>
</data>
</rpc-reply>
]]>]]>
```

Let's perform a commit operation to commit the candidate configuration: 
```
]]>]]>
<rpc><commit/></rpc>]]>]]>
<rpc-reply xmlns="urn:ietf:params:xml:ns:netconf:base:1.0" xmlns:junos="http://xml.juniper.net/junos/18.2R1/junos">
<ok/>
</rpc-reply>
]]>]]>

```

Let's use the operation get-config with a filter to retrieve the policy-options subtree from the active configuration: 
```
]]>]]>
<rpc>
    <get-config>
        <source><running/></source>
        <filter type="subtree">
            <configuration>
                <policy-options>
                </policy-options>
            </configuration>
        </filter>
    </get-config>
</rpc>
]]>]]><rpc-reply xmlns="urn:ietf:params:xml:ns:netconf:base:1.0" xmlns:junos="http://xml.juniper.net/junos/18.2R1/junos">
<data>
<configuration xmlns="http://xml.juniper.net/xnm/1.1/xnm" junos:commit-seconds="1574843507" junos:commit-localtime="2019-11-27 08:31:47 UTC" junos:commit-user="jcluser">
    <policy-options>
        <policy-statement>
            <name>bgp-in</name>
            <then>
                <accept/>
            </then>
        </policy-statement>
        <policy-statement>
            <name>bgp-out</name>
            <then>
                <accept/>
            </then>
        </policy-statement>
    </policy-options>
</configuration>
</data>
</rpc-reply>
]]>]]>

```

##### Close the NetConf session  
Let's gracefully close the NetConf session:
```
<rpc><close-session></close-session></rpc>]]>]]>
```
