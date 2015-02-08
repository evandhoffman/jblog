---
title: Setting user Postgres passwords via MD5
author: Evan Hoffman
layout: post
permalink: /2014/02/12/setting-user-postgres-passwords-via-md5/
dsq_thread_id:
  - 2960077483
categories:
  - Uncategorized
tags:
  - change password
  - hash
  - md5
  - password
  - pgsql
  - postgres
  - postgresql
  - security
---
Say you want to create a Postgres account for a user but you don&#8217;t want them to have to reset it after logging in, and you don&#8217;t want to do the &#8220;come type in a password on my computer&#8221; routine. Well, here&#8217;s one way around this. 

  1. Have the user generate an MD5 of their password on their local computer. Postgres uses the username as the salt for the hash, so the command to generate the md5 on a Mac would be (assuming a username of &#8216;ehoffman&#8217; and a password of &#8216;abcdefg&#8217;): <div class="wp_syntax">
      <table>
        <tr>
          <td class="code">
            <pre class="bash" style="font-family:monospace;"><span style="color: #7a0874; font-weight: bold;">&#91;</span>evan<span style="color: #000000; font-weight: bold;">@</span>Evans-MacBook-Pro ~<span style="color: #7a0874; font-weight: bold;">&#93;</span> $ <span style="color: #7a0874; font-weight: bold;">echo</span> <span style="color: #660033;">-n</span> abcdefgehoffman <span style="color: #000000; font-weight: bold;">|</span> md5
95eebfcce27162773a3828689df9d79e</pre>
          </td>
        </tr>
      </table>
    </div>
    
    The &#8220;-n&#8221; is important &#8211; without it, the newline gets included in the hash. After they generate their MD5, have them send it to you (along with their username). </li> 
    
      * Create the user&#8217;s account in the database (or ALTER ROLE if it already exists): <div class="wp_syntax">
          <table>
            <tr>
              <td class="code">
                <pre class="sql" style="font-family:monospace;"><span style="color: #993333; font-weight: bold;">CREATE</span> <span style="color: #993333; font-weight: bold;">ROLE</span> ehoffman LOGIN INHERIT ENCRYPTED PASSWORD <span style="color: #ff0000;">'md595eebfcce27162773a3828689df9d79e'</span>;</pre>
              </td>
            </tr>
          </table>
        </div>
        
        Syntax for an existing account:
        
        <div class="wp_syntax">
          <table>
            <tr>
              <td class="code">
                <pre class="sql" style="font-family:monospace;"><span style="color: #993333; font-weight: bold;">ALTER</span> <span style="color: #993333; font-weight: bold;">ROLE</span> ehoffman ENCRYPTED PASSWORD <span style="color: #ff0000;">'md595eebfcce27162773a3828689df9d79e'</span>;</pre>
              </td>
            </tr>
          </table>
        </div></ol> 
    
    That&#8217;s it. This has the added benefit of the password never being logged in the DB logs or the .psql_history. The main downside is the possibility of user error.