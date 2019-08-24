# mPulse-Dynamic-Alerting-Jar
Uses mPulse API to query mPulse data and compare web performance of two user-defined time periods. Output is an HTML file showing diff between two time periods, as well as XML file which can be read by Jenkins. Allows for automated check of performance after build and configuration changes to live.

## Usage: 
###Needed Values:
  mPulse API Key: Login to mPulse and open the configuration for the mPulse app in question. You will see the API key there.
  mPulse API token: Login to mPulse and go to 'Central->My Settings-> Generate Token' to create  your mPulse API token
  
### Execution
java -jar DynamicAlerting.jar baselineStartDate=-2weeks baselineEndDate=-2days baselineMultiplier=1.1 testStartDate=-3hours testEndDate=now globalThreshold=100 apiToken=<mPulseAPIToken> allowedPercentOfPageGroupsFailing=20.0 minMeasurements=10 name="mPulse Demo" apiKey=<mPulseAPIToken> tenantName="SOASTA SE Demo"
  
### Command line arguments:
	baselineStartDate=<dateFormat> - The start date for the period to derive your baseline from
	baselineEndDate=<dateFormat> -   The end  date for the period to derive your baseline from.
	baselineDomain=<domain> - The domain to measure for your baseline. Note: This requires you to setup a custom dimension called 'subDomain' in your application.
	baselineMultiplier=<float> - The multiplier above baseline time  (e.g. If baselineMultiplier=1.2  and HomePage was 3.2 for baseline, then threshold will be 3.84)
	baselineMultiplier=<float>sd - Placind 'sd' after the baseline multiplier indicates you want baseline to be based on # of standard deviations above
	                               the basedline so baselineMultiplier=1.5sd would indicate that threshold will be 1.5 standard deviations above baseline
	testStartDate=<dateFormat> - The date for your measurements to start (REQUIRED). Usually this is -5minutes to aggregate from 5 minutes ago
	testEndDate=<dateFormat> - The end dateTime for your measurement window (usually 'now') (REQUIRED) Usuaully this is 'now' to measure up until current time.
	testDomain=<domain> - The name of the domain to measure for your test measurements.Note: This requires you to setup custom dimension called 'subDomain' in your application.
	globalThreshold=<float> - A global threshold (in milliseconds) to apply to any page groups where there is no dynamic value or manual value set
	username=<string> - The username to use when logging in
	password=<string> - The password to use when requesting mPulse data
	apiToken=<string> - The apiToken of your userID (can be used instead of username/password). Found in mPulse 'My Settings'.	
	apiKey=<string> - The apiKey of the app to request data for	
	tenantName=<string> - The tenant where the app is located (only needed on multi-tenant logins	
	allowedPercentOfPageGroupsFailing=<FLOAT> - The percentage of pageGroups that can fail, and still register this job as 'Stable'  (For instance 50.0 would allow half of pageGroups to exceed threshold)
	minMeasurements=<INT> - The minimum number of measurements in the test data. If there are less measurements, we will perform a check on that pageGroup.
	threshold_<SOMENAME>=<FLOAT> - A specific hardcoded threshold value for a particular pageGroup (Example: threshold_HomePage=6.32 )
	queryModification=<STRING> - By default we use the mPulse API from here: http://docs.soasta.com/query-api/#page-groups-query-and-response to query on page group with no filter. 
		   You can modify that behavior to replace 'pagegroup?' with the string of your choice. Some common options might be: 
		   queryModification=browser?   - this will give breakdown by browsers.
		   queryModification=page-groups?country=US&   - this will filter to just US as the country.
		   queryModification=browsers?country=US&page-group=shop-category&    - This will show response times of all browsers from the US for the pageGroup of 'shop-category'
	
### Date Formats accepted:  
		MM/DD/YYYY - A specific date. If no time is specified, assumed midnight at the START of that date
		hh:mmAM-MM/DD/YY - A date and time. For example:  baselineStartDate=3:00PM-08/01/2015
		-Xdays - A relative time from right now. For instance, -3days sets time at midnight 3 days ago
		today - Today at 0:00AM  (Note: This is last midnight, not the upcoming midnight)
		-Xhours - A relative time from right now going back 'X' hours
		-Xminutes - A relative time from right now going back 'X' minutes
		now - Current time.
   
### Threshold Description:
		Thresholds are evaluated as follows:
		  If a manual threshold was set for a specific pageGroup, that value takes precedence
		  If a baselineMultiplier and baselineStartDate and baseLineEndDate was set (and no manual threshold) then the dynamic value will be calculated and used.
		  If no manual threshold or baselineMultiplier was set, then the globalThreshold value will be used.
		  Note: It's a good idea to always have some globalThreshold, as a 'failsafe' since there are situations where your baseline not have a value..
		   For example, if you have just recently created a pageGroup, your baseline won't have data to create a treshold from, and 1.2* baseline=0 sec if no baseline.


