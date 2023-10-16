<center><img src="https://github.com/xnl-h4ck3r/knoxnl/blob/main/knoxnl/images/title.png"></center>

## About

This is a python wrapper around the amazing [KNOXSS API](https://knoxss.me/?page_id=2729) by Brute Logic.
To use this tool (and the underlying API), you must have a valid KNOXSS API key. Don't have one? Go visit https://knoxss.me and subscribe!
This was inspired by the ["knoxssme" tool](https://github.com/edoardottt/lit-bb-hack-tools/tree/main/knoxssme) by @edoardottt2, but developed to allow for greater options.

## Installation

`knoxnl` supports **Python 3**.

Install `knoxnl` in default(global) python environment.

```bash
$ pip install git+https://github.com/xnl-h4ck3r/knoxnl.git
```

### pipx

Quick setup in isolated python environment using [pipx](https://pypa.github.io/pipx/)

```bash
$ pipx install git+https://github.com/xnl-h4ck3r/knoxnl.git
```

## Usage

| Arg  | Long Arg                 | Description                                                                                                                                                                                                                                                |
| ---- | ------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| -i   | --input                  | Input to send to KNOXSS API: a single URL, or file of URLs.                                                                                                                                                                                                |
| -o   | --output                 | The file to save the successful XSS and payloads to. If the file already exist it will just be appended to unless option `-ow` is passed.                                                                                                                  |
| -ow  | --output-overwrite       | If the output file already exists, it will be overwritten instead of being appended to.                                                                                                                                                                    |
| -oa  | --output-all             | Write all results to the output file, not just successful one's.                                                                                                                                                                                           |
| -X   | --http-method            | Which HTTP method to use, values `GET`, `POST` or `BOTH` (default: `GET`). If `BOTH` is chosen, then a `GET` call will be made, followed by a `POST`.                                                                                                      |
| -pd  | --post-data              | If a POST request is made, this is the POST data passed. It must be in the format `'param1=value&param2=value&param3=value'`. If this isn't passed and query string parameters are used, then these will be used as POST data if POST Method is requested. |
| -H   | --headers                | Add custom headers to pass with HTTP requests. Pass in the format `'Header1:value1;\|Header2:value2'` (e.g. separate different headers with a pipe \| character).                                                                                          |
| -A   | --api-key                | The KNOXSS API Key to use. This will be used instead of the value in `config.yml`                                                                                                                                                                          |
| -afb | --advanced-filter-bypass | If the advanced filter bypass should be used on the KNOXSS API.                                                                                                                                                                                            |
| -s   | --success-only           | Only show successful XSS payloads in the CLI output.                                                                                                                                                                                                       |
| -p   | --processes              | Basic multithreading is done when getting requests for a file of URLs. This argument determines the number of processes (threads) used (default: 3)                                                                                                        |
| -t   | --timeout                | How many seconds to wait for the KNOXSS API to respond before giving up (default: 180)                                                                                                                                                                     |
| -bp  | --burp-piper             | Use if **knoxnl** is called from the Burp Piper extension, so that a request in **Burp Suite** proxy can be tested. See the [Using in Burp Suite Proxy](#using-in-burp-suite-proxy) section below.                                                         |
| -v   | --verbose                | Verbose output                                                                                                                                                                                                                                             |
| -h   | --help                   | show the help message and exit                                                                                                                                                                                                                             |

## config.yml

The `config.yml` file (in the global location based on the OS, e.g. `~/.config/knoxnl.config.yml`) has the keys which can be updated to suit your needs:

- `API_URL` - This can be set to the KNOSS API endpoint, if and when the version number is increased
- `API_KEY` - Your KNOXSS API key that you will have generated on https://knoxss.me/

## Important Notes from KNOXSS API Guidelines

- Unlike other APIs that just retrieve data from a database, KNOXSS API returns the results like the web interface, actually performing a comprehensive vulnerability scan for XSS. Since scan results are not stored by our system, they need to be generated on the fly taking several JavaScript-evaluated live tests to return them. So it's natural the data returned takes much more time to get delivered since there's a long process involved at server side.
- The API standard rate limit is 3335 requests over a 24 hours period. That means an average of **2.3 requests per minute** so please try to keep this pace **to not overload the system**.
- **Generating or Regenerating your API Key** - The API key is in your profile. If you have never generated it you need to hit the button at least once to generate it and save. Any time you need a new API key for security reasons, you can simply hit the button and regenerate it.
- **Flash Mode Mark - [XSS]** - Provide the `[XSS]` mark in any place of the target's data values to enable Flash Mode which enables KNOXSS to perform a single quick XSS Polyglot based test.
- At the time of writing this, the daily limit of KNOXSS API calls is **3335**. If you are testing a large file of URLs,it is advisable that you use the `-o` / `--output` option to specify a file where output will be written. If you do reach the API limit with a 24 hour period, this is reset at about 17:00 GMT.
- If you use the `-o` / `--output` option and the API limit is reached part way through the input, all unchecked URLs will be output to an file in the same location, and with the same name, as the output file, but with a `.todo` suffix. You can then rename this file and use this as input at another time.
- By default, only successful results are written to the output file.
- Passing argument `-oa` / `--output-all` will write **ALL** results to the output file, not just successful one's.

## Important Notes fof knoxnl

- The KNOXSS only deals with POST requests with basic post data in the format `'param1=value&param2=value&param3=value'`.
- If the `-pd`/`--post-data` argument is not passed and a POST request is made, it will use the query string from the URL as post data if it has one.
- If a file is passed as input and POST method is required, then the post data parameters need to be provided as a query string for the URL in the file, e.g. `https://example.com?postParam1=value&postParam2-value`. If you use the `-pd`/`--post-data` with an input file then ALL URLs will use that post data.
- These are required based on the way the KNOXSS API works.

## Examples

### Basic

Pass a single URL:

```
knoxnl -i "https://brutelogic.com.br/xss.php"
```

Or a file of URLs:

```
knoxnl -i ~/urls.txt
```

### Detailed

Test a single URL for both GET and POST. if it is successful, the payload will be output to `output.txt`. In this case, an API key is provided, overriding any in `config.yml` if it exists. Also, the parameter value has been passed as `[XSS]` which will request the KNOXSS API to enable Flash Mode which performs a single quick XSS Polyglot based test:

```
knoxnl -i "https://brutelogic.com.br/xss.php?b3=[XSS]" -X BOTH -o output.txt -A 93c864f5-af3a-4f6a-8b25-8662bc8b5ab6
```

Test a single URL for POST and pass post body data:

```
knoxnl -i "https://brutelogic.com.br/xss.php" -X POST -pd user=xnl -o output.txt
```

Pass cookies and an auth header for a single URL, and use the Advanced Filter Bypass option:

```
knoxnl -i "https://bugbountytarget.com?a=one&b=2" -afb -H "Cookie: sessionId=9d7127ca-8966-4ae9-b20a-c2892a2f1167; lang=en;|Authorization: Basic eyJZb3UgZGlkbid0IHRoaW5rIHRoaXMgYSBnZW51aW5lIHRva2VuIGRpZCB5b3U/ISA7KSJ9"
```

## Using in Burp Suite Proxy

To be able to use **knoxnl** to test a request in Burp Suite Proxy, we can use it in conjunction with the amazing `Piper` extension by András Veres-Szentkirályi. Follow the steps below to set it up:

1. Go to the **BApp Store** in Burp and install the **Piper** extension.
2. Go to the **Piper** tab and click the **Context menu items** sub tab, then click the **Add** button.
3. In the **Add menu item** dialog box, enter the **Name** as `knoxnl` and change the **Can handle...** drop down to `HTTP requests only`.
4. Change both the **Minimum required number of selected items** and **Maximum allowed number of selected items** values to `1`.
5. Click the **Edit...** button for **Command** and the **Command invocation editor** dialog box should be displayed.
6. Check the **Pass HTTP headers to command** check-box.
7. If you are on a Linux machine, or Windows without WSL, do the following:
   - In the **Command line parameters** box you enter the command and arguments one line at a time.
   - You want to enter a command of `/my/path/to/python3 /my/path/to/knoxnl.py --burp-piper -X BOTH` for example, providing the full path of the `knoxnl` binary file.
   - So in the **Command line parameters** input field it would look like this:
     ```
     /my/path/to/knoxnl
     --burp-piper
     -X
     BOTH
     ```
   - You may want to add other **knoxnl** arguments too, such as `-A your_knoxss_api_key`, `-t 60`, etc. Remember to put the argument and the value on separate lines.
8. If you are on a Windows machine using WSL, do the following:
   - In the **Command line parameters** box you enter the command and arguments one line at a time.
   - You want to enter a command of `wsl -e /my/path/to/knoxnl --burp-piper -X BOTH` for example, providing the full path of the `knoxnl.py` binary file.
   - So in the **Command line parameters** input field it would look like this:
     ```
     wsl
     -e
     /my/path/to/knoxnl
     --burp-piper
     -X
     BOTH
     ```
   - You may want to add other **knoxnl** arguments too, such as `-A your_knoxss_api_key`, `-t 60`, etc. Remember to put the argument and the value on separate lines.
9. Click the **OK** button on the **Command invocation editor** dialog box.
10. Click the **OK** button on the **Edit menu item** dialog box.

Piper is now set up to be able to call **knoxnl**.

To call **knoxnl** for a particular request, follow these steps:

1. Right click on a Request and select **Extensions -> Piper -> Process 1 request -> knoxnl**.
2. A window should open with the title **Piper - knoxnl**.
3. **IMPORTANT NOTE:** This **Piper** window stays blank until the command is complete (which could be up to 180 seconds - the default value of `-t`/`--timeout`).
4. When complete, it should show the **knxonl** output in the same way as on the command line version. Just close the window when you have finished.

With **Piper** you can also send the **knoxnl** request to a queue by selecting **Extensions -> Piper -> Add to queue**. You can then go to the **Queue** sub tab under **Piper** and see the request. Right click the request to send to **knoxnl**.

## Issues

If you come across any problems at all, or have ideas for improvements, please feel free to raise an issue on Github. If there is a problem, it will be useful if you can provide the exact command you ran and a detailed description of the problem. If possible, run with `-v` to reproduce the problem and let me know about any error messages that are given, and the KNOXSS API request/response.

## Example output

Single URL:

<center><img src="https://github.com/xnl-h4ck3r/knoxnl/blob/main/knoxnl/images/example1.png"></center>

File of URLs checked with GET and POST:

<center><img src="https://github.com/xnl-h4ck3r/knoxnl/blob/main/knoxnl/images/example2.png"></center>

Good luck and good hunting!
If you really love the tool (or any others), or they helped you find an awesome bounty, consider [BUYING ME A COFFEE!](https://ko-fi.com/xnlh4ck3r) ☕ (I could use the caffeine!)

🤘 /XNL-h4ck3r
