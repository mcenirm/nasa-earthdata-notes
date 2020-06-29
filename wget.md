## Example: Bulk download using _wget_

This example shows how to download an entire year of Lightning Mapping Array data for North Alabama.

1. In a browser, [register for Earthdata Login](https://urs.earthdata.nasa.gov/users/new)
2. Pre-authorize the GHRC DAAC application
    1. In the same browser, visit a URL at GHRC DAAC that requires EDL:
       https://ghrc.nsstc.nasa.gov/pub/lma/nalma/solutions/data/
    2. Follow the prompts at EDL to authorize access to your EDL profile by GHRC DAAC.
       (Note: This seems to be automatic now.)
3. Prepare `~/.netrc` (on Windows, maybe this is `%USERPROFILE%\_netrc`?)
    ```
    machine urs.earthdata.nasa.gov  login MyEDLUserName  password MyEDLP@ssw0rd
    ```
4. Change to a suitable download location
    ```shell
    cd ~/Downloads/
    ```
5. Download a bunch of files
    ```shell
    wget \
      --auth-no-challenge \
      --cookies \
      --keep-session-cookies \
      --load-cookies=~/.edl_cookies \
      --save-cookies=~/.edl_cookies \
      --mirror \
      --no-parent \
      --reject-regex='\?C=.;O=.' \
      --output-file=bulk-download-example.log \
      https://ghrc.nsstc.nasa.gov/pub/lma/nalma/solutions/data/2016/
    ```

The example will create a directory tree, starting with `ghrc.nsstc.nasa.gov`

```
ghrc.nsstc.nasa.gov
└── pub
    └── lma
        └── nalma
            └── solutions
                └── data
                    └── 2016
                        ├── 0101
                        │   ├── index.html
                        │   ├── nalma_lylout_20160101_00_3600.dat.gz
                        │   ├── nalma_lylout_20160101_00_3600.qua.gz
                        │   ├── nalma_lylout_20160101_01_3600.dat.gz
                        │   ├── nalma_lylout_20160101_01_3600.qua.gz
                        ...
```


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
<th><code>~/.wgetrc</code> option</th>
<th>Good</th>
<th>Bad</th>
</tr>

<tr>
<td><code>--auth-no-challenge</code></td>
<td><code>auth_no_challenge = on</code></td>
<td>Lets <em>wget</em> work smoothly for any DAAC web site, even ones that are not registered as "401"-type applications in EDL</td>
<td>Might be considered bad security practice</td>
</tr>

<tr>
<td><pre>--cookies
--keep-session-cookies
--load-cookies=~/.edl_cookies
--save-cookies=~/.edl_cookies
</pre></td>
<td><pre>cookies = on
keep_session_cookies = on
load_cookies = ~/.edl_cookies
save_cookies = ~/.edl_cookies
</pre></td>
<td>Supports multiple <em>wget</em> invocations</td>
<td>Uses insecure storage</td>
</tr>

</table>



## _wget_ settings for better downloads

<table>

<tr>
<th>Command-line argument</th>
<th><code>~/.wgetrc</code> option</th>
<th>Good</th>
<th>Bad</th>
</tr>

<tr>
<td><code>--timestamping</code></td>
<td><code>timestamping = on</code></td>
<td>Avoid re-downloading files that have not changed</td>
<td></td>
</tr>

<tr>
<td>DO NOT USE <code>--no-use-server-timestamps</code></td>
<td><code>use_server_timestamps = on</code></td>
<td>Saved files get correct modification times</td>
<td></td>
</tr>

</table>
