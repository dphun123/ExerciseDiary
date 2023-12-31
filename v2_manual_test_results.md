# Example workflow

## 2. Diary Deletion

Alvin is no longer going to the gym and wants to get rid of his exercise diary. Alvin takes steroids now so he does not need to go to the gym. Alvin knows that his diary_id is 11.

Alvin starts the process of deleting diary.

Starts by calling DELETE /diary/{diary_id} with diary_id as 11.
Then it returns success since that is his diary_id.

## Testing results

- curl -X 'DELETE' \
  'https://exercisediary.onrender.com/diary/11' \
  -H 'accept: application/json'

- Response: "OK"

## 3. Getting a Past Entry and Inputting a New One

Jimmy is a daily gym goer and regular user of our exercise diary. Jimmy wants to get stronger and always tries to increase the hits max reps/weight from the last time he did the exercise. But Jimmy has Alzheimer's and can’t remember what his max reps/weight were for his squats from yesterday. Jimmy requests his diary with GET /diary/{diary_id}/{day} to get his exercises from the previous day. Today is Wednesday, so Jimmy puts inputs 13, his diary_id, and Tuesday. Jimmy will see a list of all his exercises from Tuesday. Next, Jimmy requests information for the squats exercise with GET /diary/{diary_id}/{day}/{exercise}. Jimmy will see the number of reps, weight, etc. that is associated with his squats exercise. Finally, Jimmy will input his new squat goals for today by calling POST /diary/{diary_id}/{day}/{exercise} with his stats from today’s squats.

Jimmy wants to access the reps/weight for his squats in his diary and record his stats for today’s workout. To do so he:

- First, Jimmy calls GET /diary/{diary_id}/{day} with 13 as his diary_id and Tuesday as day to get his exercises for the previous day.
- Next, Jimmy calls GET /diary/{diary_id}/{day}/{exercise} with exercise=squats to access the stats for his squats from Tuesday.
- Finally, Jimmy calls POST /diary/{diary_id}/{day}/{exercise} to update his diary with his new goal for squats for today in case he forgets again tomorrow.

Later, Jimmy sets a new pr for squats and is ready to break his pr again tomorrow, even if he forgets this new pr.

## Testing results

1.

- curl -X 'GET' \
   'https://exercisediary.onrender.com/diary/13/Tuesday' \
   -H 'accept: application/json'
  Response: ["Squats"]

2.

- curl -X 'GET' \
   'https://exercisediary.onrender.com/diary/13/Tuesday/Squats' \
   -H 'accept: application/json'
  Response: [10, 225]

3.

- curl -X 'POST' \
   'https://exercisediary.onrender.com/diary/13/Wednesday' \
   -H 'accept: application/json' \
   -H 'Content-Type: application/json' \
   -d '{
  "exercise_name": "Squats",
  "goal_reps": 11,
  "goal_weight": 225
  }'
  Response: 7 (entry_id)
