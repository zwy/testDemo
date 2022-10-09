<template>
  <div style="width: 80%; margin-left: 10%">
    <el-table :data="tableData" :span-method="arraySpanMethod" border style="">
      <el-table-column
        v-for="item in columnList"
        :prop="item.prop"
        :label="item.label"
      />
    </el-table>
  </div>
</template>

<script lang="ts" setup>
import type { TableColumnCtx } from "element-plus/es/components/table/src/table-column/defaults";

// 时长，TODO 根据需求修改值
const duration = 30; //60; //5;
// 页面上展示的开始时间
const sTime = "08:00:00";
// 页面上展示的截止时间
const eTime = "22:00:00";

// 当前日期，TODO 根据需求获取，例如点击tab切换时存缓存，重新进入页面时设置为空等逻辑。
// var currentDay = null; // 默认为null
var currentDay = "2022-05-29";

// 接口返回的值
var itemList = [
  {
    day: "2022-05-28",
    showDay: "5月28日 星期六",
    venueList: [
      {
        id: 3,
        venueCode: "f9ceaf2837a045a6bef5bd4e03f4f39a",
        name: "第一会场",
        sessionList: [],
      },
      {
        id: 4,
        venueCode: "85afee9852574233b3cf6ce79cedbec0",
        name: "第22会场",
        sessionList: [],
      },
    ],
  },
  {
    day: "2022-05-29",
    showDay: "5月29日 星期日",
    venueList: [
      {
        id: 3,
        venueCode: "f9ceaf2837a045a6bef5bd4e03f4f39a",
        name: "第一会场",
        sessionList: [
          {
            id: 2,
            sessionCode: "1269fa3a83e241b9b622af470d7356d2",
            venueCode: "f9ceaf2837a045a6bef5bd4e03f4f39a",
            startTime: "2022-05-29 08:00:00",
            endTime: "2022-05-29 18:00:00",
            sessionName: "开幕式",
            venueName: "第一会场",
          },
        ],
      },
      {
        id: 4,
        venueCode: "85afee9852574233b3cf6ce79cedbec0",
        name: "第22会场",
        sessionList: [
          {
            id: 1,
            sessionCode: "1269fa3a83e241b9b622af470d7356d2",
            venueCode: "85afee9852574233b3cf6ce79cedbec0",
            startTime: "2022-05-29 08:00:00",
            endTime: "2022-05-29 18:00:00",
            sessionName: "开幕式",
            venueName: "第22会场",
          },
        ],
      },
    ],
  },
  {
    day: "2022-05-30",
    showDay: "5月30日 星期一",
    venueList: [
      {
        id: 3,
        venueCode: "f9ceaf2837a045a6bef5bd4e03f4f39a",
        name: "第一会场",
        sessionList: [],
      },
      {
        id: 4,
        venueCode: "85afee9852574233b3cf6ce79cedbec0",
        name: "第22会场",
        sessionList: [],
      },
    ],
  },
  {
    day: "2022-05-31",
    showDay: "5月31日 星期二",
    venueList: [
      {
        id: 4,
        venueCode: "85afee9852574233b3cf6ce79cedbec0",
        name: "第22会场",
        sessionList: [],
      },
    ],
  },
];
// Columns数据
var columnList = [];
// table数组值
var tableData = [];

// 数组合并的情况
var spanMethodData = {};

// 小格子里内容，用来合并操作
var spanContentData = {};

// 将接口返回的数据格式化成需要的数据
function dataFormation(items) {
  // 1、初始化 Columns数据
  initColumnList(items);

  console.info(columnList);
  // 2、初始化 tableData、spanMethodData、spanContentData
  initTableData();

  // 3、更新 tableData
  updateTableData(items);
}

dataFormation(itemList);

function initColumnList(items) {
  let tempColumns = [];
  items.map((item) => {
    if (currentDay == null) {
      currentDay = item.day;
    }

    if (item.day == currentDay) {
      if (item.venueList.length > 0) {
        item.venueList.map((venueItem) => {
          let columnItem = {
            prop: venueItem.venueCode,
            label: venueItem.name,
          };
          tempColumns.push(columnItem);
        });
      }
    }
  });
  columnList = tempColumns;
}
function initTableData() {
  if (columnList.length <= 0) {
    return;
  }
  // 先清空
  tableData = [];
  let index = 0;
  // 总时长
  let totalMin = 14 * 60;
  let id = 1;

  let rowIndex = 0;
  while (true) {
    if (index >= totalMin) {
      break;
    }
    let columnIndex = 0;
    var item = {};
    item["id"] = id;
    columnList.map((columnItem) => {
      // 初始化设置为null
      item[columnItem.prop] = null; // "test"; //null;
      item["startTime"] = dateAddMin(index);
      item["endTime"] = dateAddMin(index + duration);
      item["sessionCode"] = null;

      if (spanMethodData[rowIndex] == null) {
        spanMethodData[rowIndex] = [];
      }
      spanMethodData[rowIndex][columnIndex] = [1, 1];

      if (spanContentData[rowIndex] == null) {
        spanContentData[rowIndex] = [];
      }
      spanContentData[rowIndex][columnIndex] = null;

      columnIndex++;
    });
    // id加1
    id++;
    // 时间+duration
    index += duration;
    // 添加到数组里
    tableData.push(item);

    //
    rowIndex++;
  }
  console.info(tableData);
  console.info(spanMethodData);
}
/**
 * 时间+
 */
function dateAddMin(duration) {
  // 当前日期的开始时间，目前是8点钟
  var date = new Date(currentDay + " " + sTime);

  //2. 获取当前分钟
  var min = date.getMinutes();
  //3. 设置当前时间+X分钟：把当前分钟数+5后的值重新设置为date对象的分钟数
  date.setMinutes(min + duration);
  // 这里返回时间类型的
  return date;
}

function updateTableData(items) {
  if (currentDay == null) {
    return;
  }
  var venueList = null;
  items.map((item) => {
    if (item.day == currentDay) {
      if (item.venueList.length > 0) {
        venueList = item.venueList;
      }
    }
  });
  if (venueList == null) {
    return;
  }

  // 填写分会场
  let rowIndex = 0;
  let columnIndex = 0;
  tableData.map((tableItem) => {
    columnIndex = 0;
    columnList.map((columnItem) => {
      // 更新
      venueList.map((venueitem) => {
        if (columnItem.prop == venueitem.venueCode) {
          // 判断不为空
          if (venueitem["sessionList"].length > 0) {
            venueitem["sessionList"].map((sessionItem) => {
              if (sessionItem.sessionCode) {
              }
              var sessionStartTime = new Date(sessionItem.startTime);
              var sessionEndTime = new Date(sessionItem.endTime);
              // 重要：仅判断tableItem的开始时间>=分会场的开始时间 & tableItem的开始时间<= 分会场的截止时间。 TODO 可根据业务需求修改判断条件
              if (
                tableItem.startTime.getTime() >= sessionStartTime.getTime() &&
                tableItem.startTime.getTime() <= sessionEndTime.getTime()
              ) {
                tableItem[columnItem.prop] = sessionItem.sessionName;
                tableItem["sessionCode"] = sessionItem.sessionCode;
                console.info(
                  "Hit" + tableItem.startTime + sessionItem.sessionName
                );
                spanContentData[rowIndex][columnIndex] =
                  tableItem[columnItem.prop];
              }
            });
          }
        }
      });
      columnIndex++;
    });
    rowIndex++;
  });
  console.info(tableData);
  // 计算合并的情况
  rowIndex = 0;
  tableData.map((tableItem) => {
    columnIndex = 0;
    columnList.map((columnItem) => {
      if (tableItem[columnItem.prop] != null) {
        // 有日程安排的
        // 判断它是不是最开始(左上角的)的那一个，如果是，则算出面积，如果不是则设置成[0, 0]
        var isFirst = checkIsFirstTd(
          rowIndex,
          columnIndex,
          tableItem[columnItem.prop]
        );
        if (isFirst) {
          spanMethodData[rowIndex][columnIndex] = calcSpanMethod(
            rowIndex,
            columnIndex,
            tableItem[columnItem.prop]
          );
        } else {
          spanMethodData[rowIndex][columnIndex] = [0, 0];
        }
      }
      columnIndex++;
    });
    rowIndex++;
  });
  console.info(spanMethodData);
}
/**
 * 检查是不是第一个
 */
function checkIsFirstTd(rowIndex, columnIndex, conetnt) {
  if (columnIndex == 0 && rowIndex == 0) {
    return true;
  } else if (rowIndex == 0 && columnIndex > 0) {
    // 判断左边的是否和自己一样，不一样返回true

    if (
      spanContentData[rowIndex][columnIndex - 1] == null ||
      spanContentData[rowIndex][columnIndex - 1] != conetnt
    ) {
      return true;
    }
  } else if (columnIndex == 0 && rowIndex > 0) {
    // 判断上边的是否和自己一样，不一样返回true
    if (
      spanContentData[rowIndex - 1][columnIndex] == null ||
      spanContentData[rowIndex - 1][columnIndex] != conetnt
    ) {
      return true;
    }
  } else {
    // 判断左边和上边的是否和自己一样，都不一样返回true
    if (
      (spanContentData[rowIndex][columnIndex - 1] == null ||
        spanContentData[rowIndex][columnIndex - 1] != conetnt) &&
      (spanContentData[rowIndex - 1][columnIndex] == null ||
        spanContentData[rowIndex - 1][columnIndex] != conetnt)
    ) {
      return true;
    }
  }
  return false;
}

/**
 * 计算一下第一个的长和宽
 */
function calcSpanMethod(rowIndex, columnIndex, conetnt) {
  let x = 1;
  let y = 1;
  let index = 1;
  while (true) {
    if (
      spanContentData[rowIndex][columnIndex + index] != null &&
      spanContentData[rowIndex][columnIndex + index] == conetnt
    ) {
      index++;
    } else {
      break;
    }
  }
  x = index;
  index = 1;
  while (true) {
    if (
      spanContentData[rowIndex + index][columnIndex] != null &&
      spanContentData[rowIndex + index][columnIndex] == conetnt
    ) {
      index++;
    } else {
      break;
    }
  }
  y = index;
  return [y, x];
}

const arraySpanMethod = ({ row, column, rowIndex, columnIndex }) => {
  // 处理数据合并的关系
  // console.info(row);
  // console.info(column);
  console.info(row + " " + column + " " + rowIndex + " " + columnIndex);
  if (spanMethodData != null && spanMethodData[rowIndex][columnIndex] != null) {
    return spanMethodData[rowIndex][columnIndex];
  }
  // 默认值
  return [1, 1];
};
</script>
<style>
.ep-table tr {
  height: 40px;
}
</style>
