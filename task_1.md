## Решение задания №1

db.showtimes.aggregate([
  {
    $addFields: {
      showDate: {
        $dateToString: {
          format: "%Y-%m-%d",
          date: { $toDate: "$startTime" }
        }
      }
    }
  },

  {
    $group: {
      _id: "$showDate"
    }
  },

  {
    $sort: {
      _id: 1
    }
  },

  {
    $project: {
      _id: 0,
      date: "$_id"
    }
  }
]);


## Предполагаемый результат

[
  { "date": "2025-03-01" },
  { "date": "2025-03-02" },
  { "date": "2025-03-04" },
  ...
]