
import boto3
import csv
client = boto3.client('ce')
from datetime import datetime

print("1.COST REPORT")
print("2.COST ANALYSIS")
print("3.COST FORECAST")
print("4.LOG REPORTS")
print("5.CPU UTILISATION\n")
choice = int(input("Enter the choice of AWS Operation: "))
if choice == 1:
    print("COST REPORT INITIALISING...\n")
elif choice == 2:
    print("COST ANALYSIS INITIALISING...\n")
elif choice == 3:
    print("COST FORECAST INITIALISING...\n")
elif choice == 4:
    print("LOG REPORTS INITIALISING...\n")
elif choice == 5:
    print("CPU UTILISATION INITIALISING...\n")
else:
    print("Please enter valid choice")

if choice == 1:
    def COST_USAGE():
        start = input("Enter the start duration in the format YYYY-MM-DD: ")
        end = input("Enter the end duration in the format YYYY-MM-DD: ")

        response1 = client.get_cost_and_usage(
            TimePeriod={
                'Start': start,
                'End': end
            },
            Granularity="DAILY",
            Metrics=['UnblendedCost', ],
            GroupBy=[
                {
                    'Type': 'DIMENSION',
                    'Key': 'SERVICE'
                },
            ],

        )

        response2 = client.get_cost_and_usage(
            TimePeriod={
                'Start': start,
                'End': end
            },
            Granularity="DAILY",
            Metrics=['UnblendedCost', ],
            GroupBy=[
                {
                    'Type': 'DIMENSION',
                    'Key': 'INSTANCE_TYPE'
                },
            ],

        )

        response3 = client.get_cost_and_usage(
            TimePeriod={
                'Start': start,
                'End': end
            },
            Granularity="DAILY",
            Metrics=[
                'UnblendedCost',
            ],
            GroupBy=[
                {
                    'Type': 'TAG',
                    'Key': 'Name'
                },
            ],

        )
        print("SERVICE REPORT")
        for j in range(100):
            try:
                print(response1["ResultsByTime"][0]["Groups"][j]["Keys"],"Amount:",response1["ResultsByTime"][0]["Groups"][j]["Metrics"]["UnblendedCost"]["Amount"])
            except IndexError:
                break
        print("INSTANCE REPORT")
        for m in range(100):
            try:
                print(response2["ResultsByTime"][0]["Groups"][m]["Keys"],"Amount:",response2["ResultsByTime"][0]["Groups"][m]["Metrics"]["UnblendedCost"]["Amount"])
            except IndexError:
                break
            
        print("INSTANCE NAME REPORT")
        for n in range(100):
            try:
                print(response3["ResultsByTime"][0]["Groups"][n]["Keys"],"Amount:",response3["ResultsByTime"][0]["Groups"][n]["Metrics"]["UnblendedCost"]["Amount"])
            except IndexError:
                break
                

        # CONVERTING THE OUTPUT TO CSV AND DOWNLOADING IT FOR SERVICE,INSTANCE TYPE,INSTANCE NAME
        
        K = []
        for i in range(1000):
            try:

                P = {'SERVICE': 's', 'AMOUNT': 1, 'INSTANCE': 's',
                     "AMOUNT1": 1, "INSTANCE NAME": 's', "AMOUNT2": 1,}
                P["SERVICE"] = response1["ResultsByTime"][0]["Groups"][i]["Keys"]
                P["AMOUNT"] = response1["ResultsByTime"][0]["Groups"][i]["Metrics"]["UnblendedCost"]["Amount"]
                P["INSTANCE"] = response2["ResultsByTime"][0]["Groups"][i]["Keys"]
                P["AMOUNT1"] = response2["ResultsByTime"][0]["Groups"][i]["Metrics"]["UnblendedCost"][
                    "Amount"]
                P["INSTANCE NAME"] = response3["ResultsByTime"][0]["Groups"][i]["Keys"]
                P["AMOUNT2"] = response3["ResultsByTime"][0]["Groups"][i]["Metrics"]["UnblendedCost"][
                    "Amount"]
                K.append(P)
            except IndexError:
                break
                

        
        fields = ["SERVICE","AMOUNT","INSTANCE","AMOUNT1","INSTANCE NAME","AMOUNT2"]
        filename = input("Enter the file name with extension as csv: ")
        
        with open(filename, 'w') as csvfile:
            writer = csv.DictWriter(csvfile, fieldnames=fields)
            writer.writeheader()
            writer.writerows(K)
 


    COST_USAGE()


elif choice == 4:
    import boto3
    from datetime import datetime

    client = boto3.client('cloudtrail')


    def LOG_REPORT():
        # Users
        A = boto3.client("iam")
        B = A.list_users()
        users = {}
        for p in range(1000):
            try:
                users[p + 1] = B["Users"][p]["UserName"]
                print(p + 1, ":", B["Users"][p]["UserName"])

            except IndexError:
                break
        # print(users)

        print("Start date")
        year = int(input("Enter the year: "))
        month = int(input("Enter the month: "))
        day = int(input("Enter the day: "))

        print("End date")
        year1 = int(input("Enter the year: "))
        month1 = int(input("Enter the month: "))
        day1 = int(input("Enter the day: "))

        c = int(input("Enter the number of the user: "))
        cp = users[c]
        print(cp)
        tt=""
        def look_events(N=False):
            response = client.lookup_events(

            LookupAttributes=[
                {
                    'AttributeKey': 'Username',
                    'AttributeValue': (cp)
                },
            ],

            StartTime=datetime(year, month, day),
            EndTime=datetime(year1, month1, day1),
            MaxResults=99,
            NextToken=N

            )

            tl = []
            for i in range(1000):
                try:

                    t = {'Username': 's', 'Event time': 1, 'Event source': 's', 'Event name': 's',
                     'AWS Region': 's',
                     "Source IP Address": 1, "Resources": 's', "Read Only": 's', "Event type": "s",
                     "Event Category": "s"}
                    tt=response['NextToken']
                    index = response["Events"][0]["CloudTrailEvent"].find("sourceIPAddress")
                    new = index + 18
                    s = ""
                    for ij in range(new, new + 15):
                        s += response["Events"][0]["CloudTrailEvent"][ij]
                

                    index1 = response["Events"][0]["CloudTrailEvent"].find("awsRegion")
                    new1 = index1 + 11
                    s1 = ""
                    for j in range(new1, new1 + 11):
                        s1 += response["Events"][0]["CloudTrailEvent"][j]
                
                
                    index2=response["Events"][0]["CloudTrailEvent"].find("eventType")
                    new2=index2
                
                    new3=+response["Events"][0]["CloudTrailEvent"].find("managementEvent")-3
            
                    s2=""
                    for k in range(new2+12, new3):
                        s2 += response["Events"][0]["CloudTrailEvent"][k]
                
                    index3=response["Events"][0]["CloudTrailEvent"].find("eventCategory")
                    new4=index3
                    new5=+response["Events"][0]["CloudTrailEvent"].find("sessionCredentialFromConsole")-3
                    s3=""
                    for k in range(new4+16, new5):
                        s3 += response["Events"][0]["CloudTrailEvent"][k]
                    
                
                
                    t["Event Category"]=s3
                    t["Event type"]=s2   
                    t["Source IP Address"] = s
                    t["AWS Region"] = s1
                    t["Read Only"] = response["Events"][i]["ReadOnly"]
                    t["Event name"] = response["Events"][i]["EventName"]
                    t["Resources"] = response["Events"][i]["Resources"]
                    t["Username"] = response["Events"][i]["Username"]
                    t['Event time'] = response["Events"][i]["EventTime"]
                    t["Event source"] = response["Events"][i]["EventSource"]
                    tl.append(t)

                except IndexError:
                    break
            fields = ['Username', 'Event time', 'Event source', 'Event name', 'AWS Region', "Source IP Address",
                  "Resources",
                  "Read Only", "Event type", "Event Category"]
            filename = input("Enter the file_name.csv: ")
            with open(filename, 'w') as csvfile:
                writer = csv.DictWriter(csvfile, fieldnames=fields)
                writer.writeheader()
                writer.writerows(tl)
        look_events(N=tt)


    LOG_REPORT()

elif choice == 3:
    client = boto3.client('ce')
    from datetime import date

    today = date.today()
    print(today)
    response = client.get_cost_forecast(
        TimePeriod={
            'Start': str(today),
            'End': input("Enter the end date: ")
        },
        Metric='UNBLENDED_COST',
        Granularity='MONTHLY',
        PredictionIntervalLevel=99
    )
    print(response)

if choice == 2:
    import boto3
    import csv

    client = boto3.client('ce')


    def COST_DIFF():
        print("ENTER THE DURATION")
        start = input("Enter the start date in the format YYYY-MM-DD: ")
        end = input("Enter the end date in the format YYYY-MM-DD: ")
        response1 = client.get_cost_and_usage(

            TimePeriod={
                'Start': start,
                'End': end
            },
            Granularity="DAILY",
            Metrics=['UnblendedCost', ],
            GroupBy=[
                {
                    'Type': 'DIMENSION',
                    'Key': 'SERVICE'
                },
            ],

        )
        t1=0
        for i in range(100):
            try:
                print(response1["ResultsByTime"][0]["Groups"][i]["Keys"], "Amount:",
                      response1["ResultsByTime"][0]["Groups"][i]["Metrics"]["UnblendedCost"]["Amount"])
                t1+=float(response1["ResultsByTime"][0]["Groups"][i]["Metrics"]["UnblendedCost"]["Amount"])
            except IndexError:
                break
        print("Total:",t1)

        print("ENTER THE DURATION")
        start = input("Enter the start date in the format YYYY-MM-DD: ")
        end = input("Enter the end date in the format YYYY-MM-DD: ")
        response2 = client.get_cost_and_usage(

            TimePeriod={
                'Start': start,
                'End': end
            },
            Granularity="DAILY",
            Metrics=['UnblendedCost', ],
            GroupBy=[
                {
                    'Type': 'DIMENSION',
                    'Key': 'SERVICE'
                },
            ],

        )
        t2=0
        for j in range(100):
            try:
                print(response2["ResultsByTime"][0]["Groups"][j]["Keys"], "Amount:",
                      response2["ResultsByTime"][0]["Groups"][j]["Metrics"]["UnblendedCost"]["Amount"])
                t2+=float(response2["ResultsByTime"][0]["Groups"][j]["Metrics"]["UnblendedCost"]["Amount"])
            except IndexError:
                break
        print("Total:",t2)
        
        print("\nCost Difference:")
        K=[]

        for k in range(200):
            try:
                P = {'SERVICE': 's', 'AMOUNT': 1, 'SERVICES':'s','AMOUNT1':1,'SERVICE NAME':'s','COST DIFFERENCE':1}
                P["SERVICE"] = response1["ResultsByTime"][0]["Groups"][k]["Keys"]
                P["AMOUNT"] = response1["ResultsByTime"][0]["Groups"][k]["Metrics"]["UnblendedCost"]["Amount"]
                P["SERVICES"] = response2["ResultsByTime"][0]["Groups"][k]["Keys"]
                P["AMOUNT1"] = response2["ResultsByTime"][0]["Groups"][k]["Metrics"]["UnblendedCost"]["Amount"]

                
            
                if (response1["ResultsByTime"][0]["Groups"][k]["Keys"] ==
                        response2["ResultsByTime"][0]["Groups"][k]["Keys"]):
                    diff = float(response1["ResultsByTime"][0]["Groups"][k]["Metrics"]["UnblendedCost"][
                                     "Amount"]) - float(
                        response2["ResultsByTime"][0]["Groups"][k]["Metrics"]["UnblendedCost"]["Amount"])
                    print(response1["ResultsByTime"][0]["Groups"][k]["Keys"], "Amount:", diff)
                    name=response1["ResultsByTime"][0]["Groups"][k]["Keys"]
                elif response1["ResultsByTime"][0]["Groups"][k]["Keys"] != \
                        response2["ResultsByTime"][0]["Groups"][k]["Keys"]:
                    print(response1["ResultsByTime"][0]["Groups"][k]["Keys"], "Amount:",
                          response1["ResultsByTime"][0]["Groups"][k]["Metrics"]["UnblendedCost"]["Amount"])
                    diff=response1["ResultsByTime"][0]["Groups"][k]["Metrics"]["UnblendedCost"]["Amount"]
                    name=response1["ResultsByTime"][0]["Groups"][k]["Keys"]
                elif response2["ResultsByTime"][0]["Groups"][k]["Keys"] != \
                        response1["ResultsByTime"][0]["Groups"][k]["Keys"]:
                    print(response2["ResultsByTime"][0]["Groups"][k]["Keys"], "Amount:",
                          response2["ResultsByTime"][0]["Groups"][k]["Metrics"]["UnblendedCost"]["Amount"])
                    diff=response2["ResultsByTime"][0]["Groups"][k]["Metrics"]["UnblendedCost"]["Amount"]
                    name=response2["ResultsByTime"][0]["Groups"][k]["Keys"]
                P["COST DIFFERENCE"]=diff
                P["SERVICE NAME"]=name
                K.append(P)
            except IndexError:
                break
        d=float(t1-t2)
        print("Difference in total:",d)
        P["SERVICE"]="TOTAL"
        P["AMOUNT"]=t1
        P["SERVICES"]="TOTAL"
        P["AMOUNT1"]=t2
        P["SERVICE NAME"]="DIFFERENCE IN TOTAL"
        P["COST DIFFERENCE"]=d
        K.append(P)

                

        
        fields = ["SERVICE","AMOUNT","SERVICES","AMOUNT1","SERVICE NAME","COST DIFFERENCE"]
        filename = input("Enter the file name with extension as csv: ")
        with open(filename, 'w') as csvfile:
            writer = csv.DictWriter(csvfile, fieldnames=fields)
            writer.writeheader()
            writer.writerows(K)
    COST_DIFF()

if choice == 5:
    import boto3

    client = boto3.client('cloudwatch')
    from datetime import datetime


    def CPU():

        b=boto3.client('ec2')
        d_instances = b.describe_instances()
        print("LIST OF INSTANCES:")
        qf = []
        ppppp=[]
        for rrp in d_instances["Reservations"]:
            eli = 0
            try:
                ppppp.append(rrp["Instances"][eli]["Tags"][0]["Value"])
                qf.append(rrp["Instances"][eli]["InstanceId"])
                eli += 1
            except IndexError:
                break
        bk = 1
        o=0
        for e in qf:
            print(bk, ":", e,":",ppppp[o])
            bk += 1
            o+=1

        qfk = int(input("Enter the appropriate number to continue: "))
        I_i = qf[qfk - 1]
        thty=ppppp[qfk-1]
        print(I_i)
    
        print("Start date")
        year = int(input("Enter the year: "))
        month = int(input("Enter the month: "))
        day = int(input("Enter the day: "))

        print("End date")
        year1 = int(input("Enter the year: "))
        month1 = int(input("Enter the month: "))
        day1 = int(input("Enter the day: "))

        response = client.get_metric_statistics(
            Namespace='AWS/EC2',
            MetricName='CPUUtilization',
            Dimensions=[
                {
                    'Name': 'InstanceId',
                    'Value': I_i
                },
            ],
            StartTime=datetime(year, month, day),
            EndTime=datetime(year1, month1, day1),
            Period=10800,
            Statistics=[
                'Average',
            ],
            Unit='Percent'

        )

        response1 = client.get_metric_statistics(
            Namespace='AWS/EC2',
            MetricName='CPUUtilization',
            Dimensions=[
                {
                    'Name': 'InstanceId',
                    'Value': I_i
                },
            ],
            StartTime=datetime(year, month, day),
            EndTime=datetime(year1, month1, day1),
            Period=10800,
            Statistics=[
                'Maximum',
            ],
            Unit='Percent'

        )

        response2 = client.get_metric_statistics(
            Namespace='AWS/EC2',
            MetricName='CPUUtilization',
            Dimensions=[
                {
                    'Name': 'InstanceId',
                    'Value': I_i
                },
            ],
            StartTime=datetime(year, month, day),
            EndTime=datetime(year1, month1, day1),
            Period=10800,
            Statistics=[
                'Minimum',
            ],
            Unit='Percent'

        )
        print("AVERAGE")
        for i in range(1000):
            try:

                print(response['Datapoints'][i]['Timestamp'], response['Datapoints'][i]['Average'],
                      response['Datapoints'][i]['Unit'])
            except IndexError:
                break
        print("MAXIMUM")
        for j in range(1000):
            try:

                print(response1['Datapoints'][j]['Timestamp'], response1['Datapoints'][j]['Maximum'],
                      response1['Datapoints'][j]['Unit'])
            except IndexError:
                break
        print("MINIMUM")
        for k in range(1000):
            try:

                print(response2['Datapoints'][k]['Timestamp'], response2['Datapoints'][k]['Minimum'],
                      response2['Datapoints'][k]['Unit'])
            except IndexError:
                break
            
        th=[]
        for i in range(1000):
            try:
                taxi={}
                print(response['Datapoints'][i]['Timestamp'],"AVERAGE:",response['Datapoints'][i]['Average'],response['Datapoints'][i]['Unit'])
                print(response1['Datapoints'][i]['Timestamp'],"MAXIMUM:",response1['Datapoints'][i]['Maximum'],response1['Datapoints'][i]['Unit'])
                print(response2['Datapoints'][i]['Timestamp'],"MINIMUM:",response2['Datapoints'][i]['Minimum'],response2['Datapoints'][i]['Unit'])
                taxi["Maximum"]=response1['Datapoints'][i]['Maximum']
                taxi["Time"]=response['Datapoints'][i]['Timestamp']
                taxi["Average"]=response['Datapoints'][i]['Average']
                taxi["Minimum"]=response2['Datapoints'][i]['Minimum']
                taxi["Server"]=thty
                th.append(taxi)
            
            except IndexError:
                break
        rf=["Server","Time","Maximum","Average","Minimum"]
        fname=input("Enter the name of the file:")
        with open(fname, 'w') as csvfile:
            wr = csv.DictWriter(csvfile, fieldnames=rf)
            wr.writeheader()
            wr.writerows(th)


    CPU()
