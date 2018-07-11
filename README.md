# httpd-enable-cors
enable  cors (cross origin resource sharing) in httpd

# Add some more related modules to load in the general httpd config file.

LoadModule alias_module modules/mod_alias.so

LoadModule actions_module modules/mod_actions.so

LoadModule headers_module modules/mod_headers.so

LoadModule env_module modules/mod_env.so

LoadModule setenvif_module modules/mod_setenvif.so

LoadModule mime_module modules/mod_mime.so

# Add a Perl script handler for OPTIONS request.
<Location /myuri>

  Script OPTIONS /cgi-bin/options.pl
  
  SetHandler myhandler
  
</Location>

# Specify the script path and alias.
<IfModule alias_module>
  
  ScriptAlias /cgi-bin/ "/usr/sap/SAPBusinessOne/ServiceLayer/cgi-bin/"
  
</IfModule>

# Grant permission to execute the script.
<Directory "/usr/sap/SAPBusinessOne/ServiceLayer/cgi-bin/">

    Options +ExecCGI
    
    Require all granted
    
    SetHandler cgi-script
    
</Directory>

# Specify the media type of the script.
<IfModule mime_module>
  
    AddHandler cgi-script .cgi .pl
    
</IfModule>

# Specify the headers about access control and the trusted origins.

<IfModule headers_module>
  
  SetEnvIf Origin "http(s)?://(www\.)?(`<trusted origin No.1>|<trusted origin No.2>`)$" AccessControlAllowOrigin=$0
  
  Header add Access-Control-Allow-Origin %{AccessControlAllowOrigin}e env=AccessControlAllowOrigin
  
  Header set Access-Control-Allow-Methods "GET, POST, PUT, PATCH, DELETE, OPTIONS"
  
  Header set Access-Control-Allow-Headers: "content-type, accept"
  
  Header set Access-Control-Allow-Credentials: "true"
  
</IfModule>

[Note] <trusted origin No.1>|<trusted origin No.2> should be replaced with your trusted origins according to your specific business requirement. For example:
        SetEnvIf Origin "http(s)?://(www\.)?(host1:8080|host2:8443)$" AccessControlAllowOrigin=$0

# Add a Perl script handler for OPTIONS request.

<Location /b1s/v1>

  Script OPTIONS /cgi-bin/options.pl
  
  SetHandler myhandler
  
</Location>

# Create a perl script handler for OPTIONS request
As indicated above, to process the OPTIONS request, a perl file (e.g. options.pl) is needed to put under the cgi-bin path of Apache. The content of options.pl is like:

#!/usr/bin/perl

print "Content-type: text/html\n\n";

print "";

# Add file mime.types
Put the file mime.types under the configuration path.
