# BluePlot
## User Interface guide
### Plot UI
![Infographic - UI](https://user-images.githubusercontent.com/7429922/214113694-722613c1-af19-4bfa-bf8f-3cb79cd2c614.png)

### Settings UI
![image](https://user-images.githubusercontent.com/7429922/214114128-f05785bc-608e-4229-a9dc-3f661a75f2c5.png)
General notes about this:
* Most of these settings directly correlate to properties of the LabVIEW XY graph, I've just wrappered them nicely.
* The most important aspect here is that all of the settings here are easily exported/imported by the Get/Load config API.  The intent here was to make it easy for users to save and load plot configuration.  I've observed that users will spend a significant amount of time customizing a plot to their liking for a particular task.
* This package only suports up to 2 y-axes: a left and a right axis.  This is why you only see 2 y-axes on the settings page.  My opinion is that multiple y-axes on the same axis can get too messy and usually means that the user should just build multiple plots.
* Note that the tick and grid line boxes are click-able and give you a picture enumeration to select from.
