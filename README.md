# BluePlot
## User Interface guide
### Plot UI
![Infographic - UI](https://user-images.githubusercontent.com/7429922/214113694-722613c1-af19-4bfa-bf8f-3cb79cd2c614.png)

### Autofill channel name UI
![image](https://user-images.githubusercontent.com/7429922/214129788-0b82c285-09d1-485f-816e-52695dcc978e.png)

A few notes about this:
- Click in the white text box to start typing in a channel name
- A listbox will appear with a list of all channel names that "MATCH PATTERN" with the current value of the user entry textbox
- Note that this listbox is a separate window, and will conveniently not be cropped by the size of the plot instance window.  (observe this in the screenshot above)
- Use up/down arrow keys to navigate the listbox
- Use return/enter keys or mouse click to make a listbox selection
- dark blue color = current listbox selection
- teal blue color = hover-over visualization for cursor

### Settings UI
![image](https://user-images.githubusercontent.com/7429922/214114128-f05785bc-608e-4229-a9dc-3f661a75f2c5.png)

#### General notes about this:
* Most of these settings directly correlate to properties of the LabVIEW XY graph control, I've just wrappered them nicely.
* The most important aspect here is that all of the settings here are easily exported/imported by the Get/Load config API.  The intent here was to make it easy for users to save and load plot configuration.  I've observed that users will spend a significant amount of time customizing a plot to their liking for a particular task.
* This package only suports up to 2 y-axes: a left and a right axis.  This is why you only see 2 y-axes on the settings page.  My opinion is that multiple y-axes on the same axis can get too messy and usually means that the user should just build multiple plots.
* Note that the tick and grid line boxes are click-able and give you a picture enumeration to select from.
* Value changes on this UI immediately propogate to the XY graph control which enables the user to see the affect of their changes immediately.  The "cancel" button will revert all changes appropriately.

### Plot area right click menu
![image](https://user-images.githubusercontent.com/7429922/214133278-5730a3e9-3591-41ad-bbc2-71eff53e35f5.png)

| menu name        | Action        | 
| ------------- |:-------------:| 
| Clear Plot Config | Resets plot to default, empty config |
| Properties | Opens the "Settings UI" |

## API
### High-level look at all the methods:
![image](https://user-images.githubusercontent.com/7429922/214123623-27da805d-14a2-4ed0-9528-7d544fe65cdf.png)

### Individual methods:

![image](https://user-images.githubusercontent.com/7429922/214123580-34f78c18-0443-46c5-bc60-f0a231be1e2a.png)

![image](https://user-images.githubusercontent.com/7429922/214123641-42095607-e501-47d5-97a0-8bd0d48a6c30.png)

![image](https://user-images.githubusercontent.com/7429922/214123677-144a96fc-8983-409b-98d5-2a94c7aec2fa.png)

![image](https://user-images.githubusercontent.com/7429922/214123711-2560a16c-3150-406e-a56c-3b4c5b09338f.png)

![image](https://user-images.githubusercontent.com/7429922/214123784-4c2e7578-3725-456f-a0c2-9368f3688b68.png)

![image](https://user-images.githubusercontent.com/7429922/214123801-29dc21a1-bc6b-42d6-a2d1-766e129b15bc.png)

## Developer Guide
If you want to use this package, then you will need to do the work desribed in the section below.

First, you will need to create your own PlotHandle class, and override required Dynamic Dispatch methods.
It's easiest to look at the shipped BluePlotHandleDemo.lvclass as an example:

![image](https://user-images.githubusercontent.com/7429922/214126756-2e5c1bf6-4648-449c-8480-3b455c57f4f3.png)

| Override filename        | Required?           | Input  | Output  |
| ------------- |:-------------:| -----:| -----:|
| Callback - Get Data by Name.vi      | Required | Name (string) | Data (to be sent to the plot) |
| Callback - Get Plottable Names.vi      | Required      |  (none) | list of plottable names (string array) |
| Callback - Subscribed Names.vi | Optional      |    (none) | list of plotted names (string array) |
| Callback - Subscribed time to plot.vi | Optional      |   (none) |selected time to plot (DBL) |

A few notes about this:
* The "plottable names" affects the list of strings that will appear in the autofill dropdown box on the plot UI
* The "Get Data by Name.vi" requires that you return the data history of a channel by name.  This means that you must have access to the history in some sort of lookup table.  The plot UI instance does not buffer or hang onto data like a LabVIEW strip-chart.  The challenge of how to buffer data is left to the end-user.  This was intentional because there are lots of ways to circularly buffer data, and I didn't want to marry data buffering to the plot UI.
* The Demo showcases how the NI "ring buffer" pacakge can be used as a possible solution for creating/maintaining a circular buffer for data, which can then be accessed by the plot UI framework.
* The "Subcribed Names" and "Subscribed time to plot" callback VIs are optional, and only exist to communicate back to the developer when one of these values changes due to user input/interaction.  This information might drive decision-making in code that maintains the circular buffers.  For example, if the user is plotting 30 minutes of a channel name, then you might want to make sure that your ring buffer is expanded to 30 minutes of storage.

## Design Decisions
### Design decision #1: Expose all native properties of the LabVIEW XY Graph control, and utilize the graph menu
![image](https://user-images.githubusercontent.com/7429922/214130823-99af60b0-57e4-4abb-a66e-8b48b774a219.png)

If you click on the little plot icons, then this gives you access to a custom XY graph property menu.
This menu is built into the XY graph control.
There are things in this menu which cannot be recreated using G-code.  Like, G-code doesn't let you include nifty pictures in a right click menu.
I decided early-on that I wanted to leverage this menu because I thought it seemed well thought-out, and I liked most of the features.
This, consequently, meant that this package must properly capture all the properties that this menu can acess/modify when I get/load config.

Another bit of weirdness:
The plot icons in the sidebar don't actually belong to the XY graph that the user sees.
This is because the plot icons and the user-viewable XY graph control are on different LabVIEW panes.
This became necessary in order to build out the concept of a collapsable sidebar.
What does this mean?
Whenever the user modifies a property via the plot icon menu, then I capture this change via a LabVIEW event, and then mirror this change over to the user-viewable XY graph.  This ended up being fairly easy given that all changes via the plot icon menu produce a LabVIEW event.  (kudos to NI for that foresight.)

### Design decision #2: Sidebar should be collapsable, maximizing the plotting space real-estate
| sidebar visible | sidebar not visible   | 
| ------------- |:-------------:|
| ![image](https://user-images.githubusercontent.com/7429922/214132419-3faa8ec6-3cc7-481d-a5c4-f8dabc7ee9bc.png) | ![image](https://user-images.githubusercontent.com/7429922/214132382-82c14bca-b9ea-42d3-8f2a-36632b4decdc.png) |

I find that users typically only need for the sidebar to be visible while they are modifying plot settings or channel names.  Otherwise, they collapse the sidebar so that screen real-estate is maximized for plotting area.

### Design decision #3: This package does not buffer data history.  This is something the developer must implement.
A LabVIEW strip-chart will buffer data history.
This makes using strip-chart very easy to implement for a developer.
You simply feed the latest value to the strip chart and allow for it to build/maintain history.
Unfortunately, this paradigm has a relatively low complexity and performance ceiling.

Conversely, this plotting tool is expected to enable the user to quickly change plotted channel names, and query this history on-demand.
This generally means that it is expected that the developer is building and maintaining data history for channels in some sort of circular buffer tool with a channel name lookup index.
Since there are many circular buffer solutions out in the world, I didn't want to marry or force the developer to use a specific circular buffer solution just to use this plotting UI.
For convenience, the DEMO showcases how the NI variant ring buffer can be used gracefully with this package.
Instead, I opted for this plotting UI to make 

### Design decision #4: The user is only able to specify a "time to plot", which represents the number of seconds to plot from the current timestamp (now)
This plotting UI is optimized for real-time, operational awareness viewing.
The intended workflow looks like this:
* DAQ acquires data (analag inputs, digital inputs, whatever you want)
* This data is shoved into a circular buffer, in memory.  The buffer might only be 30 minutes of latest-value data
* This plotting package UI accesses these circular buffers, for displaying real-time data
* The user is able to adjust time-to-plot to select how much history to plot
* "time to plot" changes are communicated in a callback to the developer, which might cause the developer to expand a ring buffer to match the user-requested history length.

### Design decision #5: Multiple plots can be asynchronosly launched.  A plot handle is used to communicate with each instance
It's expected that users might want to open and interact with multiple, concurrent plot instances.
All of this code is reentrant, and that is very much and expectation of the workflow.

Each launched plot instance returns a handle to the developer, which represents the developer's mechanism to interact with the plot instance via a plot handle API.
