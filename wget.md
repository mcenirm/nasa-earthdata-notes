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
5. Remove previous index file, if any, to avoid _wget_ quirk
    ```shell
    rm ghrc.nsstc.nasa.gov/pub/lma/nalma/solutions/data/2016/index.html
    ```
6. Choose one of these approaches to workaround _wget_ quirk involving EDL handshake and `--recursive`:
    * Single command, no cookies, repeat URL
        ```shell
        wget \
          --auth-no-challenge \
          --recursive \
          --timestamping \
          --no-parent \
          --reject-regex='\?C=.;O=.' \
          --output-file=bulk-download-example.log \
          https://ghrc.nsstc.nasa.gov/pub/lma/nalma/solutions/data/2016/ \
          https://ghrc.nsstc.nasa.gov/pub/lma/nalma/solutions/data/2016/
        ```
    * Run same command twice, using cookies
        ```shell
        wget \
          --auth-no-challenge \
          --cookies \
          --keep-session-cookies \
          --load-cookies=.edlcookies \
          --save-cookies=.edlcookies \
          --recursive \
          --timestamping \
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


## _wget_ requests and server responses

1. _wget_ requests an EDL-gated resource, such as a data file.
    1. The first time, there is no session cookie to include in the request, so the web server redirects to EDL's `/oauth/authorize` endpoint.
    2. _wget_ follows the redirect to EDL, and includes basic authentication.
    3. Assuming the authentication matches a registered profile, and the profile has already authorized the first web site, then EDL redirects back to the web site's redirection URL.
    4. _wget_ follows the redirect back to the first web site.
    5. If the first web site can verify the EDL handshake behind the scenes, then it redirects back to the original web request, providing a session cookie.
1. _wget_ requests an EDL-gated resource, such as a data file.
    1. This and subsequent times, _wget_ includes the session cookie.
    2. The web site recognizes the session cookie, so it returns the request resource.

Problems
* When _wget_ has to go through the EDL redirections, then it somehow stops doing any "recursive" requests.
* When "timestamping" is enabled and there is an existing copy in the download tree, _wget_ properly makes a "HEAD" request first, to see if the resource is newer than the downloaded copy. Unfortunately, the web server responds with "401 Unauthorized" so _wget_ gives up.
