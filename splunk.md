## splunk sample dashboard
### drill down
### clear drill down selection when filter is changed
### job.resultCount
### join 2 query into one table
### escape, field &lt;text&gt;
### makeresults to create static table

```
<form>
  <label>Checks Domain full submission by session ID</label>
  <fieldset submitButton="false" autoRun="true">
    <input type="time" searchWhenChanged="true" token="time">
      <label></label>
      <default>
        <earliest>@d</earliest>
        <latest>now</latest>
      </default>
      <change>
        <unset token="RequestId"></unset>
      </change>
    </input>
    <input type="dropdown" token="AppName">
      <label>AppName</label>
      <choice value="global-checks-blacklist-service">blacklist</choice>
      <choice value="originations-global-checks-tax-service">tax</choice>
      <default>blacklist</default>
      <change>
        <condition label="tax">
          <set token="searchText">one tax request</set>
          <set token="backend">TAX</set>
          <set token="serviceCall">TAX_REPORTING</set>
          <unset token="RequestId"></unset>
        </condition>
        <condition label="blacklist">
          <set token="searchText">one blacklist request</set>
          <set token="backend">BLACKLIST_CHECKS</set>
          <set token="serviceCall">BLACKLIST_CHECKS</set>
          <unset token="RequestId"></unset>
        </condition>
      </change>
    </input>
    <input type="text" token="elapsedTime" searchWhenChanged="true">
      <label>elapsedTime &gt; (ms)</label>
      <default>0</default>
      <change>
        <unset token="RequestId"></unset>
      </change>
    </input>
    <input type="text" token="searchText1" searchWhenChanged="true">
      <label>other search text</label>
      <default></default>
      <change>
        <unset token="RequestId"></unset>
      </change>
    </input>
    <input type="dropdown" token="joinType" searchWhenChanged="true">
      <label>joinType</label>
      <choice value="inner">inner</choice>
      <choice value="left">left</choice>
      <choice value="outer">outer</choice>
      <default>inner</default>
    </input>
  </fieldset>
  <row>
    <panel>
      <table>
        <title>Submissions $count$</title>
        <search>
          <progress>
            <set token="count">$job.resultCount$</set>
          </progress>
          <query>PCFSpace=prod AppName=$AppName$ "$searchText$" elapsedTime&gt;$elapsedTime$ 
| eval logTime=strftime(_time, "%Y-%m-%d %H:%M:%S") 
| table RequestId logTime journeyId elapsedTime
| join type=$joinType$ RequestId [search PCFSpace=prod AppName=$AppName$ backend=$backend$ serviceCall=$serviceCall$ (serviceStatus=COMPLETED OR serviceStatus=FAILED) $searchText1$
| table RequestId SessionId serviceStatus result]
| sort logTime
| table RequestId logTime SessionId journeyId elapsedTime serviceStatus result</query>
          <earliest>$time.earliest$</earliest>
          <latest>$time.latest$</latest>
        </search>
        <option name="drilldown">row</option>
        <option name="percentagesRow">false</option>
        <option name="totalsRow">false</option>
        <drilldown>
          <set token="RequestId">$row.RequestId$</set>
          <set token="SessionId">$row.SessionId$</set>
          <set token="JourneyId">$row.journeyId$</set>
        </drilldown>
      </table>
    </panel>
  </row>
  <row depends="$RequestId$">
    <panel>
      <title>Submission Details</title>
      <table>
        <search>
          <query>| makeresults
          | eval requestId="$RequestId$" 
          | eval sessionId="$SessionId$"
          | eval journeyId="$JourneyId$"
          | table requestId sessionId journeyId</query>
        </search>
        <option name="drilldown">none</option>
      </table>
      <table>
        <search>
          <query>PCFSpace=prod AppName=$AppName$ RequestId=$RequestId$ 
| rex field=_raw ".*\[LogLevel=\w+\] : (?&lt;text&gt;.*)"
| sort _time
| table _time backend serviceCall serviceStatus result elapsedTime text</query>
          <earliest>$time.earliest$</earliest>
          <latest>$time.latest$</latest>
        </search>
        <option name="count">100</option>
        <option name="drilldown">none</option>
      </table>
    </panel>
  </row>
</form>
```
