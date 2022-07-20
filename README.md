#Cost Report
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

        # CONVERTING THE OUTPUT TO CSV AND DOWNLOADING IT FOR SERVICE,INSTANCE TYPE,INSTANCE NAME
        total = 0
        tl = []
        print("TimePeriod:", response1["ResultsByTime"][0]["TimePeriod"])
        for i in range(100):
            try:
                t = {"Service": "s", "Amount": 1}
                t["Service"] = response1["ResultsByTime"][0]["Groups"][i]["Keys"]
                t["Amount"] = response1["ResultsByTime"][0]["Groups"][i]["Metrics"]["UnblendedCost"]["Amount"]
                tl.append(t)

                total += float(response1["ResultsByTime"][0]["Groups"][i]["Metrics"]["UnblendedCost"]["Amount"])
            except IndexError:
                break
        print(total)
        tfl = []
        fields = ['Service', 'Amount', '', 'Instance', 'Amount1', " ", "Instance_Name", "Amount2"]

        filename = input("Enter the file_name.csv: ")
        with open(filename, 'w') as csvfile:
            writer = csv.DictWriter(csvfile, fieldnames=fields)
            writer.writeheader()
            writer.writerows(tl)

        for i in range(100):
            try:
                tfd = {"Instance": "d", "Amount1": 2}
                tfd["Instance"] = response2["ResultsByTime"][0]["Groups"][i]["Keys"]
                tfd["Amount1"] = response2["ResultsByTime"][0]["Groups"][i]["Metrics"]["UnblendedCost"]["Amount"]
                print(response2["ResultsByTime"][0]["Groups"][i]["Keys"], "Amount",
                      response2["ResultsByTime"][0]["Groups"][i]["Metrics"]["UnblendedCost"]["Amount"])
                tfl.append(tfd)


            except IndexError:
                break

        ytl = []
        for i in range(100):
            try:
                ytd = {"Instance_Name": "h", "Amount2": 3}
                ytd["Instance_Name"] = response3["ResultsByTime"][0]["Groups"][i]["Keys"]
                ytd["Amount2"] = response3["ResultsByTime"][0]["Groups"][i]["Metrics"]["UnblendedCost"]["Amount"]
                print(response3["ResultsByTime"][0]["Groups"][i]["Keys"], "Amount",
                      response3["ResultsByTime"][0]["Groups"][i]["Metrics"]["UnblendedCost"]["Amount"])
                ytl.append(ytd)
            except IndexError:
                break

        with open(filename, 'w') as csvfile:
            writer = csv.DictWriter(csvfile, fieldnames=fields)
            writer.writeheader()
            writer.writerows(tl)
            writer.writerows(tfl)
            writer.writerows(ytl)


    COST_USAGE()
