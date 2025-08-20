---
title: 電子郵件指令碼範例
feature: Email Programs
description: Marketo電子郵件指令碼範例
source-git-commit: 3ccb27a0d184e0c1314979d404022bc4e0794f7b
workflow-type: tm+mt
source-wordcount: '52'
ht-degree: 9%

---

# 範例

您可以在下方找到一組示範性電子郵件指令碼範例。

## 事件清單

此範例使用假設性的事件自訂物件。

```html
##declare an $EventsThisYear variable
#set($EventsThisYear = 0)
<table border="1">
    <tr>
        <th>Event Name</th><th>City</th><th>Date</th>
    </tr>
    ##begin looping through EventsList
    #foreach($event in $EventsList)
    
        ##split the date field on '-'
        ##yyyy-MM-dd becomes ['yyyy','MM','dd']
        ##retrieve first array element, the year
        #set ($year = ${event.date.split("-").get(0)})
        
        ##check if $year matches designated year
        #if($year == "2013")
        
            ##increment $EventsThisYear
            #set($EventsThisYear = $EventsThisYear + 1)
                
                ##create info table row
                <tr>
                    ##add Event link
                    #set($eventUrl = "www.example.com/?event=${event.id}")
                    <td><a href="http://${eventUrl}">$event.name</a>
                    ##add Event location
                    <td>$event.location</td>
                    ##output formatted date
                    <td>$date.format('MMMM d YYYY', ${convert.parseDate($event.date, 'yyyy-MM-dd')})</td>
                </tr>
        ##end if statement
        #end
    ##close the loop
    #end
</table>
##print out total events
Total Events ${lead.FirstName} attended this Year: $EventsThisYear
```

## 格式化日期

```html
##create a date and parse it with a format
##parseDate takes two args, a date string and the format
#set($myDate = $convert.parseDate("08-07-2015", "MM-dd-yyyy"))

##format the date
##format takes two args, the format and a date object
#set($formattedDate = $date.format("yyyy-MM-dd", $myDate))

${formattedDate}
```

## 逸出可能是HTML不安全的字串

```html
##escape a string which may have HTML content
#set($HTMLSafeString = $esc.html(${SurveyList.get(0).Question}))

<div>
    <p><strong>In your survey, you asked:</strong></p>
    <p>${HTMLSafeString}</p>
</div>
```

## 將ID附加至URL

### 批次行銷活動

```html
##batch campaigns/triggers campaigns not triggers by object
##retrieves first entry from MyCustomObjectList
##then gets objectId from record and sets it as value of $objectId
#set($objectId = MyCustomObjectList.get(0).objectId)

#set($url1 = "www.example.com/objects?id=$!{objectId}")
<a href="//${url1}">View Your Object Record Online</a>
```

### 觸發程序行銷活動

```html
##trigger campaigns triggered by the needed object
##gets objectId from record and sets it as value of $objectId
#set($objectId = $TriggerObject.objectId)

#set($url2 = "www.example.com/objects?id=$!{objectId}")
<a href="//${url2}">View Your Object Record Online</a>
```
