## _wget_ and Earthdata Login

See also [How To Access Data With cURL And Wget](https://wiki.earthdata.nasa.gov/display/EL/How+To+Access+Data+With+cURL+And+Wget)

EDL authentication can be specified:
* using `--user` (or `--http-user`) option and `--ask-password`
* using `~/.netrc`
    ```
    machine urs.earthdata.nasa.gov  login MyEDLUserName  password MyEDLP@ssw0rd
    ```


## _wget_ settings for Earthdata Login

<table>

<tr>
<th>Command-line argument</th>
<th>

`~/.wgetrc` option

</th>
<th>Good</th>
<th>Bad</th>
</tr>

<tr>
<td>

`--auth-no-challenge`

</td>
<td>

`auth_no_challenge = on`

</td>
<td>

Lets _wget_ work smoothly for any DAAC web site, even ones that are not registered as "401"-type applications in EDL

</td>
<td>Might be considered bad security practice</td>
</tr>

<tr>
<td>

```
--cookies
--keep-session-cookies
--load-cookies=~/.edl_cookies
--save-cookies=~/.edl_cookies
```

</td>
<td>

```
cookies = on
keep_session_cookies = on
load_cookies = ~/.edl_cookies
save_cookies = ~/.edl_cookies
```

</td>
<td>Supports multiple _wget_ invocations</td>
<td>Uses insecure storage</td>
</tr>

</table>



## _wget_ settings for better downloads

<table>

<tr>
<th>Command-line argument</th>
<th>

`~/.wgetrc` option

</th>
<th>Good</th>
<th>Bad</th>
</tr>

<tr>
<td>

`--timestamping`

</td>
<td>

`timestamping = on`

</td>
<td>Avoid re-downloading files that have not changed</td>
<td></td>
</tr>

<tr>
<td>

DO NOT USE `--no-use-server-timestamps`

</td>
<td>

`use_server_timestamps = on`

</td>
<td>Saved files get correct modification times</td>
<td></td>
</tr>

</table>
