# Gathering Usernames from Google LinkedIn Results using Burp Pro

As part of reconnaissance when performing a penetration test, it is often useful to gather usernames. The usernames may come in handy for performing a password spraying attack for example. One easy way to gather employee names is to use a Burp Suite Pro extension with a little Python script as described in this blog. You can then massage these employee names into any username format. You may be able to discover the username format by analyzing the metadata of documents posted to a company's public web sites as described here.
To collect employee names with Burp, you'll need to do the following steps.

1.  Install the Python Scripter extension from the Extender-->BApp Store tab in Burp Suite Pro



Note: You may need to download the Jython standalone JAR file and point Burp to it if you have not done that before.



2.  Copy and paste this code into the newly available "Script" tab.

if not messageIsRequest:
  response = messageInfo.getResponse()
  analyzedResponse = helpers.analyzeResponse(response)
  headerList = analyzedResponse.getHeaders()
  bodyStr = helpers.bytesToString(response[analyzedResponse.getBodyOffset():]).encode('ascii','ignore')
  if "| LinkedIn" in bodyStr:
    strs = bodyStr.split("| LinkedIn")
    strs.pop(-1) #remove the last item
    for str in strs:
      try:
        name = (str.rsplit("x3e",1)[1]).strip()
      except:
        x="test"
      try:
        name = (str.rsplit("\">",1)[1]).strip()
        if "=\"" in name:
          name = str.rsplit("=\"",1)[1]
        if "<title>" in name:
          name = (str.rsplit("<title>",1)[1]).strip()
      except:
        x="test"
      print name



3.  Configure the Extension to save output to a file. This is where your usernames will be written. You can optionally select the "Show in UI" option, but the output window truncates items when the list gets too long.



4. Configure your browser to use Burp as a proxy as you normally would. From the browser, do a Google search of the following form (don't forget the "/in" on the end of "linkedin.com":

site:linkedin.com/in "Company Name"



The script will write the name that shows up before the text " | LinkedIn" in the search results to the output file. In this example, it would write "James Lee" and "Derek Banks". Google limits the results to 10 per page. You can click on additional pages of results to get more employee names written to the file.



You can gather a large list of employee names quickly and easily with this method. Try importing the list to Microsoft Excel where you can use formulas to turn employee names into the appropriate username format such as first initial followed by last name. It is also a good idea to use the "Remove Duplicates" functionality in Excel since the script may export the same employee name multiple times.

5.  When you are done, unload the Python Scripter or erase the script code so you don't burden Burp with inspecting all responses for the text " | LinkedIn"

