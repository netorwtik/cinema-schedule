## Решение задания №2

function getMoviesAndShowtimesForDate(selectedDate) {
  const startOfDay = new Date(selectedDate);
  startOfDay.setUTCHours(0, 0, 0, 0);
  
  const endOfDay = new Date(selectedDate);
  endOfDay.setUTCHours(23, 59, 59, 999);

  return db.showtimes.aggregate([
    {
      $match: {
        startTime: {
          $gte: startOfDay.toISOString(),
          $lte: endOfDay.toISOString()
        }
      }
    },
    {
      $lookup: {
        from: "movies",
        localField: "movieId",
        foreignField: "movieId",
        as: "movieDetails"
      }
    },
    {
      $unwind: "$movieDetails"
    },
    {
      $group: {
        _id: "$movieDetails._id",
        movieId: { $first: "$movieDetails.movieId" },
        title: { $first: "$movieDetails.title" },
        titleOriginal: { $first: "$movieDetails.titleOriginal" },
        poster: { $first: "$movieDetails.poster" },
        runtime: { $first: "$movieDetails.runtime" },
        ageRating: { $first: "$movieDetails.ageRating" },
        genres: { $first: "$movieDetails.genres" },
        showtimes: {
          $push: {
            _id: "$_id",
            showId: "$showId",
            startTime: "$startTime",
            endTime: "$endTime",
            screenId: "$screenId",
            screenName: { $concat: ["KP ", { $toString: "$screenId" }] },
            notes: "$notes"
          }
        }
      }
    },
    {
      $sort: {
        title: 1
      }
    }
  ]).toArray();
}

## Пример использования функции

const todayMoviesAndShowtimes = getMoviesAndShowtimesForDate("2025-03-08");

## Предполагаемый результат

[
  {
    "_id": "67aced5b61e6e53a9ecd5023",
    "movieId": 2275,
    "title": "1978",
    "titleOriginal": "1978",
    "poster": "https://marscode.s3.eu-north-1.amazonaws.com/assets/img/mediaserver/movies/1978-6032/poster/1739361431425",
    "runtime": 80,
    "ageRating": 15,
    "genres": ["thriller", "skrekkfilm"],
    "showtimes": [
      {
        "_id": "67aced6b052923c59e54c379",
        "showId": "2428498",
        "startTime": "2025-03-08T21:40:00.000Z",
        "endTime": "2025-03-08T23:15:00.000Z",
        "screenId": 12,
        "screenName": "KP 12",
        "notes": ["2D", "Original tale", "Engelsk tekst"]
      }
    ]
  },
  {
    "_id": "67aced5c61e6e53a9ecd5024",
    "movieId": 2276,
    "title": "Aire, Just Breathe",
    "titleOriginal": "Aire, Just Breathe",
    "poster": "https://marscode.s3.eu-north-1.amazonaws.com/assets/img/mediaserver/movies/aire-just-breathe-6833/poster/1739361972889",
    "runtime": 90,
    "ageRating": -1,
    "genres": ["sci-fi"],
    "showtimes": [
      {
        "_id": "67aced6b052923c59e54c37d",
        "showId": "2428503",
        "startTime": "2025-03-08T18:40:00.000Z",
        "endTime": "2025-03-08T20:25:00.000Z",
        "screenId": 10,
        "screenName": "KP 10",
        "notes": ["2D", "Original tale", "Engelsk tekst"]
      },
      {
        "_id": "67acfb6b052923c59e584703",
        "showId": "2428500",
        "startTime": "2025-03-08T12:15:00.000Z",
        "endTime": "2025-03-08T14:00:00.000Z",
        "screenId": 10,
        "screenName": "KP 10",
        "notes": ["2D", "Original tale", "Engelsk tekst"]
      }
    ]
  },
  и тд...
]