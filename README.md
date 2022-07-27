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
