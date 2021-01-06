---
title: Date
---

### Date

<!--more-->

```js
/*
 新的格式化日期函数。
 date：需要格式化的日期对象
 mode: 输出的日期的模式，mode可取值为:Year, Month, Day, Hour, Minute, Second其中之一。默认为Day
 dateSplitStr: 年月日之间的分隔字符串,默认为'-'
 timeSplitStr: 时分秒之间的分隔字符串,默认为':'
 */
export function formatDate(date, mode, dateSplitStr = "-", timeSplitStr = ":") {
  var year,
    month,
    day,
    hour,
    minutes,
    sec,
    rs = "",
    modeMap = {
      Year: "YYYY",
      Month: "YYYY-MM",
      Day: "YYYY-MM-DD",
      Hour: "YYYY-MM-DD hh",
      Minute: "YYYY-MM-DD hh:mm",
      Second: "YYYY-MM-DD hh:mm:ss"
    };

  if (!date || !(date instanceof Date)) {
    return;
  }
  mode = mode || "Day";
  if (!modeMap[mode]) {
    return;
  }

  year = date.getFullYear();
  month = date.getMonth();
  month = (month >= 9 ? "" : "0") + (month + 1);
  day = date.getDate();
  day = (day >= 10 ? "" : "0") + day;
  hour = date.getHours();
  hour = (hour >= 10 ? "" : "0") + hour;
  minutes = date.getMinutes();
  minutes = (minutes >= 10 ? "" : "0") + minutes;
  sec = date.getSeconds();
  sec = (sec >= 10 ? "" : "0") + sec;

  rs = year + "-" + month + "-" + day + " ";
  rs += hour + ":" + minutes;
  rs += ":" + sec;

  rs = rs.substr(0, modeMap[mode].length);
  rs = rs.replace(/-/g, dateSplitStr).replace(/:/, timeSplitStr);
  return rs;
}

//计算周一到今天的时间范围
function reduceWeekRange() {
  let date = new Date(), //当前
    curYear = date.getFullYear(), //年
    curMonth = date.getMonth() + 1, //月
    curDay = date.getDate(), //日
    dayOfWeek = date.getDay(); //今天本周的第几天
  let startDay = dayOfWeek == 0 ? curDay - 6 : curDay - dayOfWeek + 1;
  let weekStartDate = new Date(curYear, curMonth, startDay);
  let weekStr =
    weekStartDate.getMonth() +
    "月" +
    weekStartDate.getDate() +
    "日~" +
    curMonth +
    "月" +
    curDay +
    "日";
  return weekStr;
}

//当前第几周
function getYearWeek(date) {
  let yearFirstDate = new Date(date.getFullYear(), 0, 1), //对应年份第一天
    dayOfWeek = date.getDay(),
    d = 0;
  if (dayOfWeek == 0) dayOfWeek = 7;
  let yearFirstDay = yearFirstDate.getDay();
  if (yearFirstDay == 0) yearFirstDay = 7;
  d = Math.round(
    (date - yearFirstDate + (yearFirstDay - dayOfWeek) * 86400000) / 86400000
  );
  return `${Math.ceil(d / 7) + 1}`;
}

//格式化日期
function formatTime(date) {
  var year = date.getFullYear();
  var month = date.getMonth() + 1;
  var day = date.getDate();
  return [year, month, day]
    .map(n => {
      n = n.toString();
      return n[1] ? n : "0" + n;
    })
    .join("-");
}

//前n周日期范围以及第几周
function getSingleWeek(n = 1) {
  let aheadNumArr = [-7, -1, -2, -3, -4, -5, -6],
    nowDate = new Date(),
    firstDay,
    weekFirstDay,
    weekEndDay;
  let aheadNum = aheadNumArr[nowDate.getDay()];

  firstDay = new Date(nowDate.setDate(nowDate.getDate() + aheadNum)); //上周最后一天   setDate的返回值是毫秒数
  weekFirstDay = new Date(nowDate.setDate(firstDay.getDate() - 7 * n + 1)); //前n周第一天
  weekEndDay = new Date(nowDate.setDate(weekFirstDay.getDate() + 6)); //前n周最后一天

  return {
    weekNum: getYearWeek(weekFirstDay),
    start: formatTime(weekFirstDay),
    end: formatTime(weekEndDay)
  };
}

function reduceAheadMonth(i) {
  let dayResult = [],
    nowDate = new Date(),
    year = new Date().getFullYear();
  nowDate.setMonth(nowDate.getMonth() + 1, 1); //获取到当前月份,设置月份
  nowDate.setMonth(nowDate.getMonth() - i); //每次循环一次 月份值减1
  let m = nowDate.getMonth() + 1;
  let result = `${nowDate.getFullYear()}-${m < 10 ? "0" + m : m}`;
  return result;
}

function reduceAheadDate(n) {
  if (n < 1) return "";
  let currentDay = new Date(),
    oneDayTime = 24 * 60 * 60 * 1000,
    aheadDay = new Date(currentDay.getTime() + (-n + 1) * oneDayTime);
  let result = formatDate(aheadDay, "Day");
  return `${result}`;
}
```
