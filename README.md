![alt text](https://i.ibb.co/HBPZ9Nd/Rufaydium.jpg)

# Rufaydium

AutoHotkey WebDriver Library to interact with browsers.  
Rufaydium will automatically try to download the correct version.  
Supported browsers: Chrome, MS Edge, Firefox, Opera.

**Forum:** https://www.autohotkey.com/boards/viewtopic.php?f=6&t=102616

Rufaydium utilizes Rest API of W3C from https://www.w3.org/TR/webdriver2/
and also supports Chrome Devtools Protocols same as [chrome.ahk](https://github.com/G33kDude/Chrome.ahk)

## Note: 

No need to install / setup Selenium, Rufaydium is AHK's Selenium and is more flexible than selenium.

## How to use

```AutoHotkey
#Include Rufaydium.ahk
/*
	Load "chromedriver.exe" from "A_ScriptDir"
	In case Driver is not yet available, it will Download "chromedriver.exe" into "A_ScriptDir"
	before starting the Driver.
*/
Chrome := new Rufaydium("chromedriver.exe")


f1::
/*
	Create new session if WebBrowser Version Matches the Webdriver Version.
	It will ask to download the compatible WebDriver if not present.
*/
Page := Chrome.NewSession()
; navigate to url
Page.Navigate("https://www.autohotkey.com/")
return

f12::
Chrome.QuitAllSessions() ; close all session 
Chrome.Driver.Exit() ; then exits driver
return
```
# New Rufaydium(DriverName,Parameters)
Rundriver() Class integrated into Rufaydium.ahk that launches driver in the background where port 9515 set to default, 

```AutoHotkey
Chrome := new Rufaydium() ; will Download/Load Chrome driver as "chromedriver.exe" is default DriverName
MSEdge := new Rufaydium("msedgedriver.exe","--port=9516") ; will Download/Load MS Edge driver communication port will be 9516
Firefox := new Rufaydium("geckodriver.exe") ; will Download/Load geckodriver for Firefox
Opera := new Rufaydium("operadriver.exe") ; will Download/Load operadriver
```
Note: 

1. Driver will be downloaded into A_ScriptDir and old driver will be moved to A_ScriptDir "\Backup"
2. Driver will not run if Port is occupied. Make sure to not run different drivers with the same port. i.e. trying to run Chromedriver and Edgedriver with the same port.

# Driver Default port
Rufaydium now supports 4 WebDrivers and has one default port; it will not run if the Port is already in use. We need to run the driver with a separate port using Driver Parameters, or we need to exit the already running driver and run a different driver if we want to use the same port.  
Rufaydium has default ports for every driver to resolve this conflict:

|Driver Name  | Ports |
|-------------|-------|
|chromedriver | 9515  |
|msedgedriver | 9516  |
|geckodriver  | 9517  |
|operadriver  | 9518  |
|unknownDriver | 9519  |

## Driver Parameters
Parameters are WebDriver.exe CMD arguments.  
Options can vary according to different drivers and we can also check these arguments

```AutoHotkey
MsgBox, % Clipboard := RunDriver.help(Driverexelocation)

; Above MsgBox returns the following information if using chromedriver:
/*
Usage: chromedriver.exe [OPTIONS]
Options
  --port=PORT                     port to listen on
  --adb-port=PORT                 adb server port
  --log-path=FILE                 write server log to file instead of stderr, increases log level to INFO
  --log-level=LEVEL               set log level: ALL, DEBUG, INFO, WARNING, SEVERE, OFF
  --verbose                       log verbosely (equivalent to --log-level=ALL)
  --silent                        log nothing (equivalent to --log-level=OFF)
  --append-log                    append log file instead of rewriting
  --replayable                    (experimental) log verbosely and don't truncate long strings so that the log can be replayed.
  --version                       print the version number and exit
  --url-base                      base URL path prefix for commands, e.g. wd/url
  --readable-timestamp            add readable timestamps to log
  --enable-chrome-logs            show logs from the browser (overrides other logging options)
  --allowed-ips=LIST              comma-separated allowlist of remote IP addresses which are allowed to connect to ChromeDriver
  --allowed-origins=LIST          comma-separated allowlist of request origins which are allowed to connect to ChromeDriver. Using `*` to allow any host origin is dangerous!
*/
```
Hide / UnHide Driver CMD window
```AutoHotKey
Chrome := new Rufaydium()
Chrome.Driver.visible := true ; will unhide
Chrome.Driver.visible := false ; will hide
```
## Script reloading

We can reload the script as many times as we want, but the driver will be active in the process so we can have control over all the sessions created through WebDriver so far. We can also close the Driver process, but this will cause issues as we can no longer access any session created through WebDriver. its better to use `Session.exit()` followed by `Chrome.Driver.Exit()`.

```AutoHotkey
Chrome := new Rufaydium("chromedriver.exe")
; use this to close driver (after quiting all sessions) when you finished using Rufaydium
Chrome.Driver.Exit() 
```

# Capabilities Class 
One can access and use Capabilities after 'New Rufaydium()'  
Rufaydium will load Driver Capabilities according to the specified Driver.  
Makes changes to capabilities before creating a session.

```AutoHotkey
Chrome := new Rufaydium() ; will load Chrome driver with default Capabilities
Chrome.capabilities.setUserProfile("Default") ; can use Default user 
;Chrome.capabilities.setUserProfile("Profile 1") ; can change user profile

; New Session will be created according to above Capabilities settings
Session := Chrome.NewSession()
```
Capabilities can also be set manually 
```AutoHotkey
Browser := new Rufaydium()
Browser.capabilities := new capabilities(Browser,BrowserOptions,"windows",true) 
; Browser.capabilities..... other methods
```
## Enable HeadlessMode
This will SET and GET HeadlessMode
```AutoHotkey
Browser.capabilities.HeadlessMode := true
MsgBox, % Browser.capabilities.HeadlessMode
```
## Enable Incognito Mode
This will SET and GET Incognito mode
```AutoHotkey
Browser.capabilities.IncognitoMode := true
MsgBox, % Browser.capabilities.IncognitoMode
```
>Note after Setting ```IncognitoMode := true``` .setUserProfile() would not work
## Enable CrossOriginFrame
This will Set and Get CrossOriginFrame access
```AutoHotkey
Browser.capabilities.useCrossOriginFrame := true
MsgBox, % Browser.capabilities.useCrossOriginFrame
```
## Setting / Removing Args
Command-line arguments to use when starting Chrome. See [here](http://peter.sh/experiments/chromium-command-line-switches/)
```AutoHotkey
Chrome := new Rufaydium()
Chrome.capabilities.addArg("--headless")
Chrome.capabilities.RemoveArg("--headless")
```

## Binary
We can also load Chromium-based browsers, for example, the Brave browser is based on chromium and can be controlled using the ChromeDriver, SetBinary has been Merged into `NewSession(binary_location)` method

```AutoHotkey
Chrome := new Rufaydium() ; Brave browser support chromedriver.exe
; setting Brave browser 
Chrome.capabilities.Setbinary("C:\Program Files\BraveSoftware\Brave-Browser\Application\brave.exe") 
; New Session will be created using Brave browser, 
Session := Chrome.NewSession()
```
## other methods
```AutoHotkey
Chrome := new Rufaydium()
; most of the options that are included as capabilities method are defined here https://chromedriver.chromium.org/capabilities#h.p_ID_106
Chrome.capabilities.Addextensions(extensionloaction) ; will load extensions
Chrome.capabilities.AddexcludeSwitches("enable-automation") ; will load Chrome without default args
Chrome.capabilities.DebugPort(9255) ; will change port for debuggerAddress
```

# Rufaydium Sessions
## New Session
Create a session after Setting up capabilities.  
We can skip capabilities, as the session will load default Capabilities based on the Driver used. The default Capabilities should work with any Driver.  

Note: In case the WebDriver version is mismatched with the browser version, Rufaydium will ask to update the driver and update the WebDriver automatically and load the new driver and create a session.  
This ability is supported for the Chrome and MS Edge web browsers for now.

```AutoHotkey
Chrome := new Rufaydium("chromedriver.exe")
Session := Chrome.NewSession()
```

## Using WebDriver with different Browsers
Brave uses chromedriver.exe, by simply passing Browser.exe (referred binary) into NewSession() method
```AutoHotKey
Brave := new Rufaydium() ; Brave browser support chromedriver.exe
; New Session will be created using Brave browser, 
Session := Brave.NewSession("C:\Program Files\BraveSoftware\Brave-Browser\Application\brave.exe")
Brave.Session() ; will always open new Brave session until we reset 
Brave.Capabilities.Resetbinary() ; reset binary to driver default
Brave.Session() ; will create Chrome session as we have loaded Chrome driver
```
this way we can load All Chromium Based browsers

## Getting Existing Sessions
We can also access sessions created previously using the title or URL.
 
```AutoHotkey
Session := Chrome.getSession(1) ; this will return with Session by number sequencing from first created to latest created
Session := Chrome.getSession(1,2) ; this will return with first session and switch Second tab, Tabs count from left to right
Session := Chrome.getSessionByUrl(URL)
Session2 := Chrome.getSessionByTitle(Title)
```

Note: above methods are based on `httpserver\sessions` command which is not W3C standard. Rufaydium uses AHK's functions ReadIni, WriteIni & DeleteIni, to store and parse Session IDs by creating `ActiveSessions.ini` at `GeckoDriver location`, therefore `getSessionByUrl()` & `getSessionByTitle()` now support Firefox sessions too, this way Rufaydium can continue geckodriver Sessions, or multiple AHK scripts can control Firefox.

```AutoHotkey
FF := new Rufaydium("geckodriver.exe")
Page := FF.NewSession("c:\Program Files\Mozilla Firefox\firefox.exe")
```

## Session.NewTab() & Session.NewWindow()
Creates and switches to a new tab or New Window
```AutoHotkey
Session.NewTab()
Session.NewWindow()
```

## Session.Title
returns Page title
```AutoHotkey
MsgBox, % Session.Title
```

## Session.HTML
returns Page HTML
```AutoHotkey
MsgBox, % Session.HTML
```
## Session.url
return Page URL
```AutoHotkey
MsgBox, % Session.url
Session.url := "https://www.autohotkey.com/boards/posting.php?mode=edit&f=6&p=456008"
```

## Session.Refresh()
Refresh the web page and wait until it gets refreshed.
```AutoHotkey
Session.Refresh()
MsgBox, Page refresh complete
```

## Session.IsLoading
Tells if the page is ready or not by Returning a Boolean, this will be helpful for [Session.CDP()](https://github.com/Xeo786/Rufaydium-Webdriver#cdp-call)
>note: this function is not W3C standard will work only with Chromedriver
```AutoHotkey
MsgBox, % Session.IsLoading()
```
## Session.Navigate(url)
Navigates to the requested URL
```AutoHotkey
Session.Navigate("https://www.autohotkey.com/")
```
## Session.Back() & Session.Forward()
helps navigate to previous or from previous to recent the page acting like browser back and forward buttons. 

## SwitchTab(), SwitchbyTitle() & SwitchbyURL()
Help to switch between tabs.
```AutoHotkey
Session.SwitchTab(2) ; will switch tab by number, counted from left to right
Session.SwitchbyTitle(Title)
Session.SwitchbyURL(url)
```

## Session window position and location
```AutoHotkey
; Getting window position and location
sessionrect := Session.Getrect()
MsgBox, % json.dump(sessionrect)
; set session window position and location
Srect := Session.SetRect(20,30,500,400) ; x, y, w, h 
; error handling
if Srect.error
	MsgBox, % Srect.error
; setting rect will return rect array
rect := Session.SetRect(1,1) ; this maximize to cover full screen and while taking care of taskbar
MsgBox, % json.Dump(rect)
; sometime we only want to play with x or y 
Session.x := 30
MsgBox, % session.y
; this also return whole rect as well ; not just height and also 
k := Session.height := A_ScreenHeight - (A_ScreenHeight * 5 / 100)
if !k.error
	MsgBox, json.dump(k)

Session.Maximize() ; this will Maximize session window
windowrect := Session.Minimize() ; this will minimize session window
if !windowrect.error ; error handling 
	MsgBox, % json.dump(windowrect) ; if not error return with window rect

; following will turn full screen mode on
MsgBox, % Json.Dump(Session.FullScreen()) ; return with rect, you can see x and y are zero h w are full screen sizes
; this simply turn fullscreen mode of
Session.Maximize()
```

## Session.Close() and Session.Exit()
Session.Close() Close Session window
Session.Exit() terminate Session by closing all windows.

```AutoHotkey
Chrome := new Rufaydium()
Page1 := Chrome.NewSession()
Page1.Navigate("https://www.google.com/")
Page1.NewTab() 	; create new window / tab but Page1 session pointer will remain same 
Page1.Navigate("https://www.autohotkey.com/boards/viewtopic.php?t=94276") ; navigating 2nd tab
; Page1.close() ; will close the active window / tab
Page1.exit() ; will close all windows / tabs will end up closing whole session 
```

## Switching Between Window Tabs & Frame
One can Switch tabs using `Session.SwitchbyTitle(Title)` or `Session.SwitchbyURL(url="")`
but Session remains the same If you check out[above examples](https://github.com/Xeo786/Rufaydium-Webdriver#sessionnewtab--sessionnewwindow), I posted you would easily understand how switching Tab works.

Just like Switching tabs, one can Switch to any Frame but the session pointer will remain the same.

![alt text](https://i.ibb.co/PW2P9ZG/Rufaydium-Frames-Example.png)

According to the above image, we have 1 session having three tabs

Example for TAB 1

```AutoHotkey
Session.SwitchbyURL(tab1url) ; to switch to TAB 1
; tab 1 has total 3 Frame
MsgBox, % Session.FramesLength() ; this will return Frame quantity 2 from Main frame 
Session.Frame(0) ; switching to frame A
Session.getElementByID(someid) ; this will get element from frame A
; now we cannot switch to frame B directly we need to go to main frame / main page
Session.ParentFrame() ; switch back to parent frame
Session.Frame(1) ; switching to frame B
Session.getElementByID(someid) ; this will get element from frame B
; frame B also has a nested frame we can switch to frame BA because its inside frame B
Session.Frame(0) ; switching to frame BA
Session.getElementByID(someid) ; this will get element from frame BA
Session.ParentFrame() ; switch back to Frame B
Session.ParentFrame() ; switch back to Main Page / Main frame
```

Example for TAB 2

```AutoHotkey
Session.SwitchbyURL(tab2url) ; to switch to TAB 2
; tab 1 also has total 3 frames
MsgBox, % Session.FramesLength() ; this will return Frame quantity 3
Session.Frame(0) ; switching to frame X
Session.ParentFrame() ; switch back to Main Page / Main frame
Session.Frame(1) ; switching to frame Y
Session.ParentFrame() ; switch back to Main Page / Main frame
Session.Frame(2) ; switching to frame Z
Session.ParentFrame() ; switch back to Main Page / Main frame
```

Example for TAB 3

```AutoHotkey
Session.SwitchbyURL(tab3url) ; to switch to TAB 3
MsgBox, % Session.FramesLength() ; this will return Frame quantity which is Zero because TAB 3 has no frame
```
>Note: Switching frame would not work for [Session.CDP](https://github.com/Xeo786/Rufaydium-Webdriver#cdpframes)

## Error Handling
Error Handling works with all methods, except methods that return Element pointer Few common functionalities

## Accessing Element / Elements
Following methods return with element pointer. Fails will return empty and do not support error handling
```AutoHotkey
Element := Session.getElementByID(id)
Element := Session.QuerySelector(Path)
Element := Session.QuerySelectorAll(Path)
Element := Session.getElementsbyClassName(Class)
Element := Session.getElementsbyName(TagName) : same as getElementsbyTagName(TagName)
Element := Session.getElementsbyXpath(xPath)
```
Getting element(s) from the element Just like DOM
```AutoHotkey
element := Session.QuerySelector(Path)[0]       
subelements := element.QuerySelector(Path)[0]
```
Above function are simply based on findelement()/ findelements()
```AutoHotkey
Session.findelement(by.selector,"selectorparameter") 
Session.findelements(by.selector,"selectorparameter") 
```
We can check the element's length
```AutoHotKey
elements := Session.QuerySelector(Path)
MsgBox, % elements.count()
```

See [accessing table](https://github.com/Xeo786/Rufaydium-Webdriver#accessing-tables)

## by Class

```AutoHotkey
Class by
{
	static selector := "css selector"
	static Linktext := "link text"
	static Plinktext := "partial link text"
	static TagName := "tag name"
	static XPath	:= "xpath"
}
```

## Accessing Tables

There are many ways to access the table you can use the JavaScript function to extract `Session.ExecuteSync(JS)` or `Session.CDP.Evaluate(JS)`
but an easy and simple way is to utilize AHK `for` loops. Looping through the table is a little bit slow because one Rufaydium step consists of 3 steps

1) `Json.Dump()` 
2) `WinHTTP Request` 
3) `Json.load()` 

Looping through tables takes lots of steps, so it's better to use `Session.ExecuteSync(JS)` to read huge tables and do it much faster if we just want to extract table data and do not have to interact with tables 

>Note: Following method will only works when InnerText return with tabs and line breaks
```AutoHotkey
; reading thousand rows lighting fast
Table := Session.QuerySelectorAll("table")[1].innerText
Tablearray := []
for r, row in StrSplit(Table,"`n") 
{
	for c, cell in StrSplit(row,"`t")
	{
		;MsgBox, % "Row: " r " Col:" C "`nText:" cell
		Tablearray[r,c] := cell
	}
}
MsgBox, % Tablearray[1,5]
```

## Session.ActiveElement()
returns handle for focused/active element, this function can also act as a bridge between Session.CDP and Session.Basic
```AutoHotkey
CDPelement.focus()
element := Session.ActiveElement() ; now we have access of element which we previously focused using CDP
```

## Handling Session alerts popup messages
```AutoHotkey
Session.Alert("GET") ; getting text from pop up msg
Session.Alert("accept") ; pressing OK / accept pop up msg
Session.Alert("dismiss") ; pressing cancel / dismiss pop up msg
Session.Alert("Send","some text")  ; sending a Alert / pop up msg 
```

## Tacking Screen Shots accept only png file format
```AutoHotkey
Session.Screenshot("picture location.png") ; will save PNG to A_ScriptDir
Session.Screenshot(a_desktop "\picture location.png") ; will save PNG to a_desktop
```

## PDF printing 
Supported only for headless mode according to WebDriver.
```AutoHotkey
Session.print(PDFlocation,PrintOptions.A4_Default) ; see Class PrintOptions
Session.print(PDFlocation,{"":""}) ; for default print options
```

## Class PrintOptions
PrintOptions to make custom PrintOptions
```AutoHotkey
Class PrintOptions ; https://www.w3.org/TR/webdriver2/#print
{
	static A4_Default =
	( LTrim Join
	{
 	"page":{
 		"width": 50,
 		"height": 60
	},
 	"margin":{
 		"top": 2,
 		"bottom": 2,
 		"left": 2,
 		"right": 2
	},
 	"scale": 1,
 	"orientation": "portrait",
	"shrinkToFit": json.true,
 	"background": json.true
	}
	)
}
```

## Session inputs events

```AutoHotkey
Session.move(x,y) move mouse pointer to location
Session.click() ; sending left click on moved location ; [button: 0(left) | 1(middle) | 2(right)]
Session.DoubleClick() ; sending double left click on moved location ; [button: 0(left) | 1(middle) | 2(right)]
Session.MBDown() ; sending mouse left click down on moved location ; [button: 0(left) | 1(middle) | 2(right)]
Session.MBup() ; sending mouse left click up on moved location ; [button: 0(left) | 1(middle) | 2(right)]
; now you can understand how to drag and drop stuff  read about element location rect and size further down below 
```

## Session Cookies

```AutoHotkey
Session.GetCookies() ; return with object array of cookies you need to parse then and understand 
Session.GetCookieName(Name) ; return with cookie with Name haven't tested it 
Session.AddCookie(CookieObj) ; will add cookie idk request parameters for adding cookies
```
Use JSON.Dump() to determine the cookie's attributes.
```AutoHotkey
Msgbox, %  JSON.Dump(Session.GetCookies())
/*
[{"domain": ".autohotkey.com", "expiry": 1654584141, "httpOnly": 0, "name": "_gat_gtag_UA_5170375_17", "path": "/", "secure": 0, "value": "1"}, 
{"domain": ".autohotkey.com", "expiry": 1654670481, "httpOnly": 0, "name": "_gid", "path": "/", "secure": 0, "value": "GA1.2.1414453342.1654584081"}, 
{"domain": ".autohotkey.com", "expiry": 1717656081, "httpOnly": 0, "name": "_ga", "path": "/", "secure": 0, "value": "GA1.2.1530957962.1654584081"}]
*/
```
An example of retrieving all cookies. Some results may return blank if the cookie doesn't have that attribute.

```AutoHotkey
cookies := Session.GetCookies() ; https://developer.chrome.com/docs/extensions/reference/cookies/#type-Cookie
Loop % cookies.Length()
{
    MsgBox, % cookies[A_Index].Domain	; .autohotkey.com	
    MsgBox, % cookies[A_Index].Expiry	; 1654584321	
    MsgBox, % cookies[A_Index].HostOnly ; 
    MsgBox, % cookies[A_Index].HttpOnly ; 0
    MsgBox, % cookies[A_Index].Name	; _gat_gtag_UA_1234567_89
    MsgBox, % cookies[A_Index].Path	; /
    MsgBox, % cookies[A_Index].SameSite	;    
    MsgBox, % cookies[A_Index].Secure   ; 0
    MsgBox, % cookies[A_Index].Session  ; 
    MsgBox, % cookies[A_Index].StoreId  ; 
    MsgBox, % cookies[A_Index].Value    ; 1
}
```
An example of retrieving a single cookie by name.
```AutoHotkey
var := Session.GetCookieName("CFID")
MsgBox, % var.Domain " | " var.Expiry " | " var.Value ; etc.
```

# WDElement

Getting Elements and their Information 

```AutoHotkey
Element.Name() ; will return tagname
Element.Rect() ; will return position and size
Element.Size() ; will return Size
Element.Location() ; will return position
Element.LocationInView() ; will return position in view
Element.enabled() ; will return Boolean true for enabled or false disabled 
Element.Selected() ; will return Boolean true for Selected or false not selected this will come handy for dropdown lists or combo list selecting options
Element.Displayed() ; will return Boolean true for visible element / false for invisible element

; inputs and event triggers 
Element.Submit() ; this will trigger existing event(s)
Element.SendKey("text string " . key.class ) ; this convert text and will send key event to element and see Key.class for special keys 
Element.SendKey(key.ctrl "a" key.delete) ; this will clear text content in edit box by simply doing Ctrl + A and  delete
Element.Click() ; sent simple click
Element.Move() ; move mouse pointer to that element it will help drag drop stuff see session.click and session.move 
Element.clear() ; will clear selected item / uploaded file or content text 
; getting value  element should be input box or editable other wise you need to use Session.CDP approach 
; to modify element selenium user will understand what I am talking about
MsgBox, % Element.value
;setting value 
Element.value := "somevalue"

Element.InnerText ; return with innerText 
; you cannot set innerText using this approach you will need Session.CDP approach , CDP can modify whole DOM 
; if element is innerText based edit box we can simply use Element.SendKey()

; Attribs properties & CSS
Element.GetAttribute(Name) ; return with required attribute
Element.GetProperty(Name) ; return with required Property
Element.GetCSS(Name) ; return with CSS

; element Shadow
Element.Shadow() ; return with shadow element detail actually I going to add functionality to access shadow elements in future
; first I need to learn about them

Element.Uploadfile(filelocation) ; this not working right now, I am working on it, I need to find out Payload/request parameters 
Element.Sendkey(StrReplace(filelocation,"\","/")) ; if Element is input element than file location can be set using SendKey()
; click on upload button now initiate fileupload, after setting file location 
```

## Shadow Elements
Shadow elements can easily be accessed using `element.shadow()`.
The following example will navigate to the Chrome extensions page and enables Developer mode
```AutoHotKey
Chrome := new Rufaydium()
Page := Chrome.getSessionByUrl("chrome://extensions")
if !isobject(page)
{
	Page := Chrome.NewSession()
	Page.Navigate("chrome://extensions")
}
page.QuerySelector("extensions-manager").shadow().QuerySelector("extensions-toolbar").shadow().getelementbyid("devMode").click()
```
## Key.Class

```AutoHotkey
Class Key
{
	static Unidentified := "\uE000"
	static Cancel:= "\uE001"
	static Help:= "\uE002"
	static Backspace:= "\uE003"
	static Tab:= "\uE004"
	static Clear:= "\uE005"
	static Return:= "\uE006"
	static Enter:= "\uE007"
	static Shift:= "\uE008"
	static Control:= "\uE009"
	static Ctrl:= "\uE009"
	static Alt:= "\uE00A"
	static Pause:= "\uE00B"
	static Escape:= "\uE00C"
	static Space:= "\uE00D"
	static PageUp:= "\uE00E"
	static PageDown:= "\uE00F"
	static End:= "\uE010"
	static Home:= "\uE011"
	static ArrowLeft:= "\uE012"
	static ArrowUp:= "\uE013"
	static ArrowRight:= "\uE014"
	static ArrowDown:= "\uE015"
	static Insert:= "\uE016"
	static Delete:= "\uE017"
	static F1:= "\uE031"
	static F2:= "\uE032"
	static F3:= "\uE033"
	static F4:= "\uE034"
	static F5:= "\uE035"
	static F6:= "\uE036"
	static F7:= "\uE037"
	static F8:= "\uE038"
	static F9:= "\uE039"
	static F10:= "\uE03A"
	static F11:= "\uE03B"
	static F12:= "\uE03C"
	static Meta:= "\uE03D"
	static ZenkakuHankaku:= "\uE040"	
}
```
# Session.Actions()
We can interact with page using `Actions()` these interactions, which can be define as `pointer`, a `pointer` can be mouse, touch or keyboard
Following Example will try to draw a pentagon for working example see https://www.autohotkey.com/boards/viewtopic.php?f=6&t=102616&start=40#p458272

```AutoHotKey
; defining Action object
Pentagon =
( LTrim Join
{
"actions": [
	{
	"type": "pointer",
	"id": "mouse",
	"parameters": {"pointerType": "mouse"},
	"actions": [
		{"type": "pointerDown", "button": 0},
		{"type": "pointerMove", "duration": 10,"x":288, "y":258},
		{"type": "pointerMove", "duration": 10,"x":391, "y":181},
		{"type": "pointerMove", "duration": 10,"x":493, "y":258},
		{"type": "pointerMove", "duration": 10,"x":454, "y":358},
		{"type": "pointerMove", "duration": 10,"x":328, "y":358},
		{"type": "pointerMove", "duration": 10,"x":288, "y":258},
		{"type": "pointerUp", "button": 0}
		]
		}
	]
}
)
Session.actions(json.load(Pentagon)) ; initiate actions 
return
```

# Await

Rufaydium Basic will wait for any task/change to get completed, and then execute the next line but any task executed through CDP `Session.CDP` would wait, therefore we need to use `Session.CDP.WaitForLoad()`

Waiting of webpage is based on document ready state https://www.w3schools.com/jsref/prop_doc_readystate.asp
but there are web pages that keep loading and unloading elements and stuff while their ready state remains `complete`, 
In this kind of situation Rufaydium Basic and Rufaydium CDP would simply wait through error or if an element in question is not available or element visibility or displayed/enabled stats element, displayed(), element.enabled(), we can use these tricks to make AutoHotkey wait, 
for example, We have click button and this would load element with tag name button.

```AutoHotkey
while !IsObject(button) ; 
{
   sleep, 200
   ; getting element do not support error handling for now but they do return with element object if found and empty when find nothing
   button := Session.QuerySelector("button") 
}
h := button.innerText
while h.error
{
    h := button.innerText ; but element.methods support error handling
    sleep, 200
}
MsgBox, % "innerText" h ; otherwise h has innertext
Button.click()
```
## Session.CDP
`Session.Methods()` act like Human interactions with Webpage, Where Session.CDP has access to Chrome Devtools protocols, which have the power to Modify DOM.

Example
```AutoHotkey
ChromeDriver := A_ScriptDir "\chromedriver.exe"
; in case driver is already running it will get access driver which is already running
Driver := new RunDriver(ChromeDriver) 
Chrome := new Rufaydium(Driver)
Page := Chrome.getSessionByUrl(Webpage) ; getting session

if !isobject(Page)
{
	MsgBox, no session found
	return
}

; Page.CDP.Document()	; no longer needed
input := Page.CDP.QuerySelector(".mb-2")
MsgBox, % input.innerText
for k , tag in  Page.cdp.QuerySelectorAll("input") ; full all input boxes with their ids
{
	tag.sendKey(tag.id)
}
```
Note: Firefox / Geckodriver session does not support Session.CDP (Chrome Devtools Protocols) as Firefox has its Remote protocols, which will be added soon as Session.FRP, Firefox Remote Protocols

# CDP.Document()
CDP.Document() is DEPRECATED is no longer required as Rufaydium CDP has developed reliable access to frame

# CDP functionalities
```AutoHotkey
Session.CDP.navigate(url) ; navigate to url
Session.CDP.WaitForLoad() ; unlike Session.methods() CDP does not support await

; getting element
element := Session.CDP.querySelector(selector) 
element := Session.CDP.getElementByID(ID)
; getting array or elements
elements := Session.CDP.querySelectorAll(selector) 
elements := Session.CDP.getElementsbyClassName(Class)
elements := Session.CDP.getElementsbyName(Tagename)

/* getting element by JS function 
1) GetelementbyJS() can only be used on Document like Document.GetelementbyJS(JS), yes it will work on element 
   but it would consider document as base node / pointer
2) The JS should return with element or array of elements i.e. GetelementbyJS("document.querySelectorAll('input')")
if you want to pass function and want use results from it then you can pass your function which should return with 
   one element or array of elements like this
3) you can use GetelementbyJS(js).value := var and GetelementbyJS(js)[].value := var it totally depends on you 
   JavaScript what you are passing,
4) you can't do something like this GetelementbyJS("document.querySelector('input').value = '1234'") there is 
   CDP.Evaluate() for that
5) What I think GetelementbyJS() is slow we should use DOM.querySelector for fast results but JavaScript users 
   would understand that why I have made GetelementbyJS(), in some scenarios JS get results more faster, 
   like above I mentioned below passing JS custom function using Evaluate,
*/
element := Session.CDP.GetelementbyJS("JSfunc()") ; JS funct

; get element by location
; this method does not reriued Session.CDP.Document()
element := Session.CDP.getelementbyLocation(x,y)
```
# CDP.Element
Following methods only applicable to element(s) return from CDP 
```AutoHotkey
CDP_element.getBoxModel()	; with Json array of element coord margins and paddings detail
CDP_element.getNodeQuads()	; quads are x immediately followed by y for each point, points clock-wise

val := CDP_element.value	; get value
CDP_element.value := "abcd"	; set value

eleClass := CDP_element.class	; get Class
CDP_element.class := "abcd"	; set Class

eleID := CDP_element.id		; get id
CDP_element.id := "abcd"	; set id

text := CDP_element.innerText	; get innerText
CDP_element.innerText := "abcd"	; set innerText

text := CDP_element.textContent	; get textContent

html := CDP_element.OuterHTML	; get html
CDP_element.OuterHTML := htmlstring	; set html

allattribus := CDP_element.getAttributes() ; gets all the attributes as Object we can use json dump to see whats inside
value := CDP_element.getAttribute(Name) ; getting specific attribute value base on above method
CDP_element.setAttribute(Name,Value) : change attribute value

; this uses dispatch event with istrusted parameter true
CDP_element.focus()
CDP_element.click() ; send click()
CDP_element.ClickCoord(x,y, delay:= 10) ; send click to a coord
CDP_element.SendKey("1234`n") ; send 1 2 3 4 enter
```

# CDP Evaluate(JS)
`Session.CDP.Evaluate()` executes Javascript, just like we use Chrome console.
```AutoHotkey
js = 
(
function findByTextContent(searchText)
{
var aTags = document.querySelectorAll("[Class='mb-4 block-menu-item col-xl-auto col-lg-4 col-sm-6 col-12']");
var found;
for (var i = 0; i < aTags.length; i++) {
  if (aTags[i].textContent == searchText) {
    found = aTags[i];
    break;
  }
}
return found
}
)
Session.CDP.evaluate(js)
Session.CDP.evaluate("findByTextContent('" btnName "').childNodes[0].click()")
```

# CDP.Frames
We can switch to the frame using CDP methods Just like Basic.
```AutoHotkey
MsgBox, % Page.CDP.FramesLength() ; will return child frame length
Page.CDP.Frame(0) ; switched to Frame 1
Page.CDP.ParentFrame() ; switched back to Main page / frame
```
# CDP Call
Call is `sendCommand call` for Chrome Devtools protocols, https://chromedevtools.github.io/devtools-protocol/ 
all above methods are Based on CDP.Call() `Session.CDP.call(method,Json_param)`
```AutoHotkey
ExtList := ["*.ttf","*.gif" , "*.png" , "*.jpg" , "*.jpeg" , "*.webp"]
Session.CDP.call("Network.enable")
Session.CDP.call("Network.setBlockedURLs",{"urls": ExtList })
```
