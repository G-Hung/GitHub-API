"""
30/1/2016

This scripy is consisted of 3 parts:

1. ExtractDataFromURL(host, user, repos, query)
For the given elements, construct URL and return data in JSON format

2. MonitorEvent(user, repos, checked)
Retreive event stream from GitHub API, simulate webhook if event title is updated

3. Run the result
"""

import urllib
import json
import time

"""1. ExtractDataFromURL(host, user, repos, query)"""
def ExtractDataFromURL(host, user, repos, query):
#Setup and retreive the data from API url
#[hostname] + [user] + [repository] + [query]
#eg: ['https://api.github.com/repos'] + ['G-Hung'] + ['hello-world'] + ['/issues/events']

    url=str(host)+'/'+str(user)+'/'+str(repos)+str(query)
    uh=urllib.urlopen(url)
    return uh.read()

"""2. MonitorEvent(user, repos, checked)"""
def MonitorEvent(user, repos, checked):
#Specify user name and repository in GibHub and which events did we check before
#The function will return simulated issue update webhook or NO update if #event in stream doesn't change

    #Set the stage
    host='https://api.github.com/repos'#The task is for github API so the hostname will be this one
    
    #Extract event stream in json format
    stream=ExtractDataFromURL(host, user, repos, '/issues/events')

    #Parse event stream  
    Eventjs=json.loads(str(stream))
    print 'Retrieved event stream from "%s/%s" with'%(user,repos), len(Eventjs), 'events and', len(stream),'characters'

    #Check if there is new update, if yes, check in detail whether title is changed, else return no change
    if len(Eventjs)>checked:
        print "The event is updated"
        result=list()
        #For one event stream, there are len(js) events, only process the new events [len(js)-checked]
        for i in range(0,len(Eventjs)-checked):
            
            #if an event didn't process before [Not in result], Ensure only process the latest events
            if (Eventjs[i]['issue']['number']) not in result:
                
                #mark this event as processed and put it to result
                result.append(Eventjs[i]['issue']['number'])
                
                #Check if the event is title change
                if Eventjs[i]['event']=='renamed':
                    print 'title is changed:\n', 'From:', Eventjs[i]['rename']['from'], '\nTo: ', Eventjs[i]['rename']['to']
            
                    #If yes, start to construct the components similar to webhook structure            
                    issue=ExtractDataFromURL(host, user, repos, '/issues/'+str(Eventjs[i]['issue']['number']))#issue
                    repository=ExtractDataFromURL(host, user, repos,'')#repository
                    try: sender=json.loads(issue)["user"]#sender
                    except: sender=None
            
                    #combine the components into dictionary and dump it to JSON format
                    SimulatedWebhook={'action':'updated','issue':str(issue),'repository':str(repository),'sender':str(sender)}
                    SimulatedWebhook=json.dumps(SimulatedWebhook)
                    
                    #********The simulated Webhook should be send to target server which I don't know how to do********
                    #********The simulated Webhook should be send to target server which I don't know how to do********
                    #relay(SimulatedWebhook,ServerAddress)
                    #********The simulated Webhook should be send to target server which I don't know how to do********
                    #********The simulated Webhook should be send to target server which I don't know how to do********

                    print 'webhook is simulated for issue', Eventjs[i]['issue']['number'], ' in %s/%s\n'%(user, repos)                  
        
        #return #event to update checked so we don't need to check every event next time
        return len(Eventjs)
        
    else:
        print "Event stream has no update"
        return len(Eventjs)

"""3. Run the result"""

#Initialize dict checked
checked={} #"checked" is dict that records what events are checked in previous observations


#Set the stage
host='https://api.github.com/repos'#The task is for github API so the hostname will be this one
user='G-Hung'#Pull the data from this user
repos='hello-world'#Pull the data from this repository in this user

start_time = time.time()#Measure the time difference
#Check whether this repository exists, print url if not exist
try:
    print json.loads(ExtractDataFromURL(host, user, repos, ''))['message']
    print "It seems this repository has some problems: %s/%s" %(user,repos)
    print "Take a look: %s" % str(host)+'/'+str(user)+'/'+str(repos)+''
    
#If repository exists, monitor new generated event stream from previous time using checked, update checked after check
except:
    temp=checked.get(user+'/'+repos,0)
    checked[user+'/'+repos]=MonitorEvent(user, repos, checked.get(user+'/'+repos,0))
    print "checked is updated for %s/%s from %d to %d" %(user,repos,temp,checked[user+'/'+repos])

#main()
print("--- %s seconds ---" % (time.time() - start_time))
