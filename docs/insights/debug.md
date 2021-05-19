---
id: debug
title: Using Extended Debugging
sidebar_label: Debugging
description: Extended debugging functionality toggles a number of additional functionality in WebDriver tests
---
import useBaseUrl from '@docusaurus/useBaseUrl';
import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

Enable Extended Debugging when you create your session to allow for additional functionality with Chrome and Firefox.

:::note
Extended Debugging generates additional assets (Console Logs and HAR files) that impact test performance,
so you may not want to use it as a default unless you need specific functionality.
:::

:::note
Extended Debugging is not supported with Headless.
:::

## Enable Extended Debugging

To turn on Extended Debugging, set the `extendedDebugging` capability to `true` when creating your session.
When a test with extended debugging enabled completes,
you can access the logs and files through the Sauce Labs UI or with the REST API.

### Using Chrome

Extended Debugging is supported in the 3 most recent versions of Chrome.
Chrome supports all the features described on this page.

Here is code for using Extended Debugging in Chrome:

:::note
Check out [Test Configurations](https://wiki.saucelabs.com/display/DOCS/Test+Configuration+Options) for a complete list of valid Capabilities
:::

<Tabs
  defaultValue="java"
  values={[
    {label: 'Java', value: 'java'},
    {label: 'Node', value: 'node'},
    {label: 'Python', value: 'python'},
    {label: 'Ruby', value: 'ruby'},
    {label: 'C#', value: 'c-sharp'},
 ]}>

<TabItem value="java">

```
MutableCapabilities sauceOptions = new MutableCapabilities();
sauceOptions.setCapabilitiy("extendedDebugging", true);

ChromeOptions options = new ChromeOptions();
options.setBrowserVersion("latest-1");
options.setPlatformName("Windows 10");
options.setCapability("sauce:options", sauceOptions);
```
</TabItem>
<TabItem value="node">

```
const capabilities: {
  browserName: 'chrome',
  browserVersion: 'latest',
  platformName: 'Windows 10',
  'sauce:options': {
        extendedDebugging: true,
  }
}
```

</TabItem>
<TabItem value="python">

```
capabilities = {
  'browserName': 'chrome',
  'browserVersion': 'latest-1',
  'platformName': 'Windows 10',
  'sauce:options': {
    'extendedDebugging': true
  }
}
```

</TabItem>
<TabItem value="ruby">

```
capabilities = {
  browser_name: 'chrome',
  browser_version: 'latest-1',
  platform_name: 'Windows 10',
  'sauce:options': {
    extended_debugging: true
  }
}
```

</TabItem>
<TabItem value="c-sharp">

```
var sauceOptions = new Dictionary<string, object>();
sauceOptions.Add("extendedDebugging", true);

var options = new ChromeOptions
{
    BrowserVersion = "latest-1",
    PlatformName = "Windows 10"
};

options.AddAdditionalCapability("sauce:options", sauceOptions);
```

</TabItem>
</Tabs>

### Using Firefox

Extended Debugging is supported in Firefox versions 53 and above
Firefox only supports working with [HAR Files](#har-files) and [Console Logs](#javascript-js-console-logs).

You can use Extended Debugging just like you do in [Chrome](#using-chrome) 
(just replace `"chrome"` with `"firefox"` in those examples).
If you are using a custom profile, though, you need to add a few extra settings.

:::note
These preferences and arguments adjust the information you can see in the `about:config` page in Firefox.
:::

<Tabs
defaultValue="java"
values={[
{label: 'Java', value: 'java'},
{label: 'Node', value: 'node'},
{label: 'Python', value: 'python'},
{label: 'Ruby', value: 'ruby'},
{label: 'C#', value: 'c-sharp'},
]}>

<TabItem value="java">

```
MutableCapabilities sauceOpts = new MutableCapabilities();
sauceOpts.setCapability("extendedDebugging", true);

FirefoxOptions options = new FirefoxOptions();
options.setCapability("browserVersion", "87");
options.setCapability("platformName", "windows 10");
options.setCapability("sauce:options", sauceOpts);

FirefoxProfile profile = new FirefoxProfile();
options.setProfile(profile);
options.addPreference("devtools.debugger.remote-enabled", true);
options.addPreference("devtools.debugger.prompt-connection", false);
options.addPreference("devtools.chrome.enabled", true);
options.addArguments("-start-debugger-server=9222");
```
</TabItem>
<TabItem value="node">

```
const capabilities: {
  browserName: 'firefox',
  browserVersion: 'latest',
  platformName: 'Windows 10',
  'sauce:options': {
        extendedDebugging: true,
  },
  'moz:firefoxOptions': {
    'profile': '<CUSTOM_PROFILE_ID>',
    'args': [
        '-start-debugger-server',
        '9222'
    ],
    prefs: {
        'devtools.debugger.remote-enabled': true,
        'devtools.debugger.prompt-connection': false,
        'devtools.chrome.enabled': true
    }
  }
}
```

</TabItem>
<TabItem value="python">

```
sauceOptions = {"extendedDebugging": True}

options = webdriver.FirefoxOptions()
options.set_capability('platformName', 'Windows 10')
options.set_capability('browserVersion', '87')
options.profile = webdriver.FirefoxProfile()
options.set_preference('devtools.debugger.remote-enabled', True)
options.set_preference('devtools.debugger.prompt-connection', False)
options.set_preference('devtools.chrome.enabled', True)
options.add_argument('-start-debugger-server=9222')

options.set_capability("sauce:options", sauceOptions)
```

</TabItem>
<TabItem value="ruby">

```
sauce_opts = {'sauce:options': {extendedDebugging: true}}
caps = Selenium::WebDriver::Remote::Capabilities.firefox(sauce_opts)

profile = Selenium::WebDriver::Firefox::Profile.new
opts = {platform_name: 'Windows 10',
       browser_version: '88',
       args: ['-start-debugger-server', '9222'],
       prefs: {'devtools.debugger.remote-enabled' => true,
               'devtools.debugger.prompt-connection' => false,
               'devtools.chrome.enabled' => true},
       profile: profile}
firefox_options = Selenium::WebDriver::Firefox::Options.new(opts)

capabilities = [caps, firefox_options]
```

</TabItem>
<TabItem value="c-sharp">

```
var sauceOptions = new Dictionary<string, object>();
sauceOptions.Add("extendedDebugging", true);

var options = new FirefoxOptions
{
    BrowserVersion = "latest-1",
    PlatformName = "Windows 10"
};

options.AddAdditionalCapability("sauce:options", sauceOptions);
```

</TabItem>
</Tabs>

## Use Custom Execute Script Commands
Selenium's [Execute Script](https://www.selenium.dev/documentation/en/webdriver/browser_manipulation/#execute-script) 
feature lets you execute JavaScript commands from your test code to perform actions in the browser. 
Sauce Labs provides custom commands to work with Extended Debugging features via JavaScript execution.

:::note
For working examples of these features, see our
[Extended Debugging Example Code](https://github.com/saucelabs-training/demo-js/tree/master/webdriverio/webdriver/examples/extended-debugging).
:::

Things you can do with the custom scripts:
* Redirect requests (e.g. load a separate page or portion of page)
* Block responses (e.g. prevent loading 3rd party vendor code)
* Replace responses (e.g. Mock responses)
* Insert or change response headers
* Return error from request
* Simulate running your app on a slower system
* Simulate different connectivity conditions that may affect the performance of your app
* Access network logs during test (e.g. you can make an assertion that a specific asset has loaded)

Here are explanations for each of these:

### `sauce:intercept` + redirect

Configure `sauce:intercept` to redirect an outgoing request to an alternate URL.

**Command Parameters**

<table id="table-fw">
  <tbody>
    <tr>
     <td><code>url</code></td>
     <td><p><small>| REQUIRED | STRING |</small></p><p>An outgoing request URL. Wildcards are supported, for example <code>https://saucelabs.com/rest/v1/*</code> would redirect any v1 rest API requests.</p></td>
    </tr>
  </tbody>
  <tbody>
    <tr>
     <td><code>redirect</code></td>
     <td><p><small>| REQUIRED | STRING |</small></p><p>An absolute URL to where the original request is redirected. The code samples use <code>https://google.com</code>.</p></td>
    </tr>
  </tbody>
</table>

**Examples**

<Tabs
    groupId="lang"
    defaultValue="python"
    values={[
      {"label":"Python","value":"python"},
      {"label":"WebdriverIO","value":"wdio"}
    ]}>
<TabItem value="python">

```py
driver.execute_script('sauce:intercept', {
    "url": "https://saucelabs.com",
    "redirect": "https://google.com"
})
```

</TabItem>
<TabItem value="wdio">

```js
browser.interceptRequest({
    "url": "https://saucelabs.com/",
    "redirect": "https://google.com"
})
```

</TabItem>
</Tabs>

### `sauce:intercept` + response

Configure `sauce:intercept` to return the specified response.

**Command Parameters**

<table id="table-fw">
  <tbody>
    <tr>
     <td><code>url</code></td>
     <td><p><small>| REQUIRED | STRING |</small></p><p>An outgoing request URL. Wildcards are supported, for example <code>https://saucelabs.com/rest/v1/*</code> would redirect any v1 rest API requests.</p></td>
    </tr>
  </tbody>
  <tbody>
    <tr>
     <td><code>response</code></td>
     <td><p><small>| REQUIRED | STRING |</small></p><p>An object that defines the response to send when the requested URL is intercepted. Valid attributes of the response are:</p>
     <ul>
      <li><code>status</code>: HTTP response code representing the request status, such as <code>200</code>.</li>
      <li><code>headers</code>: List of key/value pairs set as header variables, for example, <code>Cookie: "foo=bar"</code>.</li>
      <li><code>body</code>: Actual response payload to return.</li>
    </ul></td>
    </tr>
  </tbody>
</table>

**Examples**

<Tabs
    groupId="lang"
    defaultValue="python"
    values={[
      {"label":"Python","value":"python"},
      {"label":"WebdriverIO","value":"wdio"}
    ]}>
<TabItem value="python">

```py
driver.execute_script("sauce:intercept", {
    "url": "http://sampleapp.appspot.com/api/todos",
        "response": {
            "headers": {
                "x-custom-header": "foobar"
            },
            "body": [{
                "title": "Hello",
                "order": 1,
                "completed": false,
                "url": "http://todo-backend-express.herokuapp.com/15727"
            }]
        }
    })
```

</TabItem>
<TabItem value="wdio">

```js
browser.interceptRequest({
    "url": "http://sampleapp.appspot.com/api/todos",
        "response": {
            "headers": {
                "x-custom-header": "foobar"
            },
            "body": [{
                "title": "Hello",
                "order": 1,
                "completed": false,
                "url": "http://todo-backend-express.herokuapp.com/15727"
            }]
        }
    })
```

</TabItem>
</Tabs>

### `sauce:intercept` + error

Configure `sauce:intercept` to return the specified error.

**Command Parameters**

<table id="table-fw">
  <tbody>
    <tr>
    <td><code>url</code></td>
    <td><p><small>| REQUIRED | STRING |</small></p><p>An outgoing request URL. Wildcards are supported, for example <code>https://saucelabs.com/rest/v1/*</code> would redirect any v1 rest API requests.</p></td>
    </tr>
  </tbody>
  <tbody>
    <tr>
    <td><code>error</code></td>
    <td><p><small>| REQUIRED | STRING |</small></p><p>A valid error status to return when the requested URL is intercepted. Valid values are:</p>
     <ul>
      <li><code>Failed</code></li>
      <li><code>Aborted</code></li>
      <li><code>TimedOut</code></li>
      <li><code>AccessDenied</code></li>
      <li><code>ConnectionClosed</code></li>
      <li><code>ConnectionReset</code></li>
      <li><code>ConnectionRefused</code></li>
      <li><code>ConnectionAborted</code></li>
      <li><code>ConnectionFailed</code></li>
      <li><code>NameNotResolved</code></li>
      <li><code>InternetDisconnected</code></li>
      <li><code>AddressUnreachable</code></li>
    </ul></td>
    </tr>
  </tbody>
</table>

**Examples**

<Tabs
    groupId="lang"
    defaultValue="python"
    values={[
      {"label":"Python","value":"python"},
      {"label":"WebdriverIO","value":"wdio"}
    ]}>
<TabItem value="python">

```py
driver.execute_script("sauce:intercept", {
    "url": "https://saucecon.com/wp-content/uploads/2017/07/SauceCon-hero-img-100-2.jpg",
    "error": "Failed"})
```

</TabItem>
<TabItem value="wdio">

```js
browser.interceptRequest({
    "url": "https://saucecon.com/wp-content/uploads/2017/07/SauceCon-hero-img-100-2.jpg",
    "error": "Failed"})
```

</TabItem>
</Tabs>

### `sauce:throttleCPU`

Mobile devices have less CPU power than most desktops and laptops (or a VM's default configuration). 
Use CPU Throttling to simulate how your app will run on slower systems.

**Command Parameters**

<table id="table-fw">
  <tbody>
    <tr>
    <td><code>rate</code></td>
    <td><p><small>| REQUIRED | STRING |</small></p><p>A number defining the amount of slowdown (e.g. <code>2</code> ~ 2x slowdown, your CPU will be 2 times slower than usual).</p></td>
    </tr>
  </tbody>
</table>

**Examples**

<Tabs
    groupId="lang"
    defaultValue="python"
    values={[
      {"label":"Python","value":"python"},
      {"label":"WebdriverIO","value":"wdio"}
    ]}>
<TabItem value="python">

```py
driver.execute_script('sauce:throttleCPU', {
    "rate": 4
})
```

</TabItem>
<TabItem value="wdio">

```js
browser.throttleCPU(4)
```

</TabItem>
</Tabs>

### `sauce:throttleNetwork`

With network conditioning you can test your site on a variety of network connections, including Edge, 3G, and even offline. You can throttle the data throughput, including the maximum download and upload throughput, and use latency manipulation to enforce a minimum delay in connection round-trip time (RTT).

**Command Parameters**

<table id="table-fw">
  <tbody>
    <tr>
    <td><code>condition</code></td>
    <td><p><small>| REQUIRED | STRING |</small></p><p>A string or object representing browser network conditions. Sauce Labs supports the following predefined network profiles:</p>
    <table>
     <tr>
      <th>Profile</th>
      <th>Download Speed (kb/s)</th>
      <th>Upload Speed (kb/s)</th>
      <th>Round Trip Time (ms)</th>
    </tr>
    <tr>
      <td><code>offline</code></td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <td><code>GPRS</code></td>
      <td>50</td>
      <td>20</td>
      <td>500</td>
    </tr>
    <tr>
      <td><code>Regular 2G</code></td>
      <td>250</td>
      <td>50</td>
      <td>300</td>
    </tr>
    <tr>
      <td><code>Good 2G</code></td>
      <td>450</td>
      <td>150</td>
      <td>150</td>
    </tr>
    <tr>
      <td><code>Regular 3G</code></td>
      <td>750</td>
      <td>250</td>
      <td>100</td>
    </tr>
    <tr>
      <td><code>Good 3G</code></td>
      <td>1Mb/s</td>
      <td>750</td>
      <td>40</td>
    </tr>
    <tr>
      <td><code>Regular 4G</code></td>
      <td>4Mb/s</td>
      <td>3Mb/s</td>
      <td>20</td>
    </tr>
    <tr>
      <td><code>DSL</code></td>
      <td>2Mb/s</td>
      <td>1Mb/s</td>
      <td>5</td>
    </tr>
    <tr>
      <td><code>WiFi</code></td>
      <td>30Mb/s</td>
      <td>15Mb/s</td>
      <td>2</td>
    </tr>
    <tr>
      <td><code>online</code></td>
      <td>No Restrictions</td>
      <td>No Restrictions</td>
      <td>No Restrictions</td>
    </tr>
  </table>
  <p>Alternatively, you can define an object specifying individual parameters for each of the three network values, as shown in the code examples:</p>
  <ul>
    <li><code>download</code></li>
    <li><code>upload</code></li>
    <li><code>latency</code></li>
  </ul>
  </td>
  </tr>
  </tbody>
</table>

**Examples**

<Tabs
    groupId="lang"
    defaultValue="python"
    values={[
      {"label":"Python","value":"python"},
      {"label":"WebdriverIO","value":"wdio"}
    ]}>
<TabItem value="python">

```py
driver.execute_script('sauce:throttleNetwork', {
    "condition": "GPRS"
})
# or
driver.execute_script('sauce:throttleNetwork', {
    "condition": {
        "download": 1000,
        "upload": 500,
        "latency": 40
    }
})
```

</TabItem>
<TabItem value="wdio">

```js
browser.throttleNetwork("GPRS")
// or
browser.throttleNetwork({
        "download": 1000,
        "upload": 500,
        "latency": 40
})
```

</TabItem>
</Tabs>

### `sauce:log` + `sauce:network`

Extended Debugging allows Sauce to record all network requests made by the open page in the browser during the test. 

**Command Parameters**

<table id="table-fw">
  <tbody>
    <tr>
    <td><code>type</code></td>
    <td><p><small>| REQUIRED | STRING |</small></p><p>The type of log to collect. Currently only "sauce:network" is supported.</p></td>
    </tr>
  </tbody>
</table>

**Examples**

<Tabs
groupId="lang"
defaultValue="wdio"
values={[
{"label":"WebdriverIO","value":"wdio"}
]}>
<TabItem value="wdio">

```js
driver.execute('sauce:log', { type: 'sauce:network' })
```

</TabItem>
</Tabs>

## JavaScript (JS) Console Logs

The JS console collects security errors, warnings, and messages that are explicitly logged by the browser. 
You can use these logs to find out which components of your web application failed to load or ran into an error, 
what warnings were logged by the browser, and get more information about application performance. 
The console log information is associated with the URL where it occurred, and is composed of four types of information: 
**Log**, **Info**, **Warning**, and **Error**. In this example, you can see how an error was generated for the URL 
`https://pbs.twimg.com/profile_images/477099293250052097/fMFjb8gu_400x400.jpeg` when a resource failed to load:

```
{
   "http://webdriver.io/api/action/click.html":[
      {
         "level":"error",
         "column":0,
         "text":"Failed to load resource: the server responded with a status of 404 (OK)",
         "source":"network",
         "networkRequestId":"1543.182",
         "url":"https://pbs.twimg.com/profile_images/477099293250052097/fMFjb8gu_400x400.jpeg",
         "timestamp":1501197041.22091,
         "line":0,
         "type":"log"
      }
   ]
}
```

### Accessing Console Logs

Access the JS Console logs (`console.json`) under the **Logs** tab of the **Test Details** page in the Sauce Labs application, or through the REST API by calling the [assets endpoint](/dev/api/jobs#get-a-job-asset-file):

```
curl --compressed -O https://{SAUCE_USERNAME}:{SAUCE_ACCESS_KEY}@{DATA_CENTER}.saucelabs.com/v1/eds/JOB_ID/console.json
```

## HAR Files

HTTP Archive Format (HAR) files collect all network requests and responses made and received by the browser during testing. HAR files offer useful debugging information, such as:

* Identifying browser requests that time out
* Pinpointing requests slowing down the loading process
* Locating faulty API calls

### Viewing HAR Files

You need a HAR viewer to read HAR files. Sauce Labs provides a [React Network Viewer](https://opensource.saucelabs.com/network-viewer/) HAR viewer for your convenience. For step-by-step usage instructions, see our [Visualize HAR Files with the New React Network Viewer](https://opensource.saucelabs.com/blog/react_network_viewer/) blog article.

Alternatively, some other commonly used HAR viewers include:

* [HTTP Archive Viewer](http://www.softwareishard.com/har/viewer/) (*Software is Hard* blog)
* [Chrome HAR Viewer](https://ericduran.github.io/chromeHAR/) (*Google, by Eric Duran*)

### Accessing HAR Files

To download HAR files from the Sauce Labs application:

1. From your Sauce Labs dashboard, navigate to **Live > Test Results**.
1. Select the applicable test.
1. Click the **Metadata** tab.

You can also download a HAR file programmatically using the following API request:

```
curl --compressed -O https://{SAUCE_USERNAME}:{SAUCE_ACCESS_KEY}@{DATA_CENTER}.saucelabs.com/v1/eds/JOB_ID/network.har
```
