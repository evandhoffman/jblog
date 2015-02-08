---
title: Installing a new SSL certificate in your ELB via CLI
author: Evan Hoffman
layout: post
permalink: /2014/10/14/installing-a-new-ssl-certificate-in-your-elb-via-cli/
categories:
  - Uncategorized
tags:
  - aws
  - certificate
  - digicert
  - elb
  - iam
  - openssl
  - ssl
  - x509
---
For future me:

  1. Create the key and CSR: <div class="wp_syntax">
      <table>
        <tr>
          <td class="code">
            <pre class="bash" style="font-family:monospace;"><span style="color: #666666;">$ </span>openssl req <span style="color: #660033;">-out</span> wildcard.site.com.csr <span style="color: #660033;">-new</span> <span style="color: #660033;">-newkey</span> rsa:<span style="color: #000000;">2048</span> <span style="color: #660033;">-nodes</span> <span style="color: #660033;">-keyout</span> wildcard.site.com.key</pre>
          </td>
        </tr>
      </table>
    </div>

  2. Upload the CSR to your SSL vendor (in this case, DigiCert) and obtain the signed SSL certificate.
  3. Create a PEM-encoded version of the signing key. This is required for AWS/IAM certs. To check if your key is already PEM-encoded, just &#8220;head -1 site.key&#8221;. If the first line says &#8220;&#8212;&#8211;BEGIN PRIVATE KEY&#8212;&#8211;&#8221; then it&#8217;s NOT PEM-encoded. The first line should be &#8220;&#8212;&#8211;BEGIN RSA PRIVATE KEY&#8212;&#8211;&#8220;. <div class="wp_syntax">
      <table>
        <tr>
          <td class="code">
            <pre class="bash" style="font-family:monospace;">$ openssl rsa <span style="color: #660033;">-in</span> wildcard.site.com.key <span style="color: #660033;">-outform</span> PEM <span style="color: #660033;">-out</span> wildcard.site.com.pem.key
writing RSA key</pre>
          </td>
        </tr>
      </table>
    </div>

  4. Upload the certificate to the IAM keystore: <div class="wp_syntax">
      <table>
        <tr>
          <td class="code">
            <pre class="bash" style="font-family:monospace;">$ aws iam upload-server-certificate <span style="color: #660033;">--server-certificate-name</span> star_site_20141014 <span style="color: #660033;">--certificate-body</span> file:<span style="color: #000000; font-weight: bold;">///</span>Users<span style="color: #000000; font-weight: bold;">/</span>evan<span style="color: #000000; font-weight: bold;">/</span>certs_20141014<span style="color: #000000; font-weight: bold;">/</span>site<span style="color: #000000; font-weight: bold;">/</span>certs<span style="color: #000000; font-weight: bold;">/</span>star_site_com.crt <span style="color: #660033;">--private-key</span> file:<span style="color: #000000; font-weight: bold;">///</span>Users<span style="color: #000000; font-weight: bold;">/</span>evan<span style="color: #000000; font-weight: bold;">/</span>certs_20141014<span style="color: #000000; font-weight: bold;">/</span>wildcard.site.com.pem.key <span style="color: #660033;">--certificate-chain</span> file:<span style="color: #000000; font-weight: bold;">///</span>Users<span style="color: #000000; font-weight: bold;">/</span>evan<span style="color: #000000; font-weight: bold;">/</span>certs_20141014<span style="color: #000000; font-weight: bold;">/</span>site<span style="color: #000000; font-weight: bold;">/</span>certs<span style="color: #000000; font-weight: bold;">/</span>DigiCertCA.crt
<span style="color: #7a0874; font-weight: bold;">&#123;</span>
    <span style="color: #ff0000;">"ServerCertificateMetadata"</span>: <span style="color: #7a0874; font-weight: bold;">&#123;</span>
        <span style="color: #ff0000;">"ServerCertificateId"</span>: <span style="color: #ff0000;">"XXXXXXXXXXXXXXX"</span>,
        <span style="color: #ff0000;">"ServerCertificateName"</span>: <span style="color: #ff0000;">"star_site_20141014"</span>,
        <span style="color: #ff0000;">"Expiration"</span>: <span style="color: #ff0000;">"2017-12-18T12:00:00Z"</span>,
        <span style="color: #ff0000;">"Path"</span>: <span style="color: #ff0000;">"/"</span>,
        <span style="color: #ff0000;">"Arn"</span>: <span style="color: #ff0000;">"arn:aws:iam::9999999999:server-certificate/star_site_20141014"</span>,
        <span style="color: #ff0000;">"UploadDate"</span>: <span style="color: #ff0000;">"2014-10-14T15:29:28.164Z"</span>
    <span style="color: #7a0874; font-weight: bold;">&#125;</span>
<span style="color: #7a0874; font-weight: bold;">&#125;</span></pre>
          </td>
        </tr>
      </table>
    </div>

Once the above steps are complete, you can go into the web console (EC2 -> Load Balancers), select the ELB whose cert you want to change, click the &#8220;Listeners&#8221; tab, click the SSL port (443) and select the new cert from the dropdown.