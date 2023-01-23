# BluePlot
## User Interface guide
### Plot UI
![Infographic - UI](https://user-images.githubusercontent.com/7429922/214113694-722613c1-af19-4bfa-bf8f-3cb79cd2c614.png)

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
