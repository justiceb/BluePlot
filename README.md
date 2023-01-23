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
### Design decision: 
