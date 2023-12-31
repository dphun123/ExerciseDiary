# API Specification

## 1. Diary Creation

1. `New Diary`
2. `Add Exercise`
3. `Add Entry`

### 1.1. New Diary - `/diary/` (POST)

Creates a new diary.

**Request**:

```json
{
  "user_id": "string" /* potentially used for authentication */,
  "days": "list[str]" /* can be any amount of days, custom-named, etc. */,
  "copy_id": "string" /* existing diary_id to copy or NULL*/
}
```

**Returns**:

```json
{
  "diary_id": "int" /* used for future calls */
}
```
**Errors**:

1. Tried To Create An Empty Diary
```
Status: 422 Unprocessable Entity
Detail: "Your diary must contain at least one day."
```

### 1.2.1 Add Exercise to Diary on a Specific Day - `/diary/{diary_id}/{day}/` (PUT)

Adds an exercise to a specific day in the diary. This would be included in all future entries.

**Request**:

```json
{
  "exercise": "string",
  "goal_weight": "int",
  "goal_reps": "int"
}
```

**Returns**:

```json
{
  "success": "boolean"
}
```

**Errors**:

1. No Diary
```
Status: 404 Does Not Exist
Detail: "A diary with this id does not exist."
```

2. Not The Diary Owner
```
Status: 401 Unauthorized
Detail: "You do not own this diary."
```

3. Incorrect Combination
```
Status: 404 Does Not Exist
Detail: "This combination of diary and day id's does not exist."
```
4. Exercise Does Not Exist
```
Status: 404 Does Not Exist
Detail: "An exercise with this name does not exist. View possible exercises with the exercises endpoint."
```

### 1.2.2 Remove Exercise from Diary on a Specific Day - `/diary/{diary_id}/{day}/{exercise}` (DELETE)

Removes an exercise from a specific day in the diary. This would remove all current entries as well.

**Request**:

```json
{}
```

**Errors**:

1. No Entry
```
Status: 404 Does Not Exist
Detail: "An entry with this id does not exist."
```

2. Not The Entry Owner
```
Status: 401 Unauthorized
Detail: "You did not create this entry."
```
3. Tried To Create An Empty Diary
```
Status: 422 Unprocessable Entity
Detail: At least one value must be edited."
```
4. No Exercise
```
Status: 404 Does Not Exist
Detail: "An exercise with this name does not exist. View possible exercises with the exercises endpoint."
```

**Returns**:

```json
{
  "success": "boolean"
}
```

### 1.3. Edit Diary - `/entry_id` (PATCH)

**Request**:

```json
{
  "weight": "int",
  "reps": "int",
  "comments": "string" /* optional */
}
```

**Returns**:

```json
{
  "success": "boolean"
}
```

**Errors**:

1. No Entry
```
Status: 404 Does Not Exist
Detail: "An entry with this id does not exist."
```

2. Not The Entry Owner
```
Status: 401 Unauthorized
Detail: "You did not create this entry."
```

## 2. Diary Deletion

1. `Delete Diary`

### 2.1. Delete Diary - `/diary/{diary_id}` (DELETE)

**Returns**:

```json
{
  "success": "boolean"
}
```

**Errors**:

1. No Diary
```
Status: 404 Does Not Exist
Detail: "A diary with this id does not exist."
```

2. Not The Diary Owner
```
Status: 401 Unauthorized
Detail: "You do not own this diary."
```

## 3. Get Exercise and Recommendations

1. `Suggest Specific Type Excercise`

### 3.1 Suggest Specific Type Excercise - `/diary/{diary_id}{exercises}` (GET)

**Request**

```json
{
  "type": "string"
}
```

**Returns**:

```Json
{
    "exercises": "string[]",
    "weight": "int",
    "reps": "int"
}
```

## 4. Get Previous Exercise Entry

1. `Get Diary`
2. `Get Day`
3. `Get Exercise`
4. `Get Diaries Minimized`

### 4.1. Get Diary - `/diary/{diary_id}` (GET)

**Returns**:

```json
{
  "days": "int[]"
}
```
**Errors**:

1. No Diary
```
Status: 404 Does Not Exist
Detail: "A diary with this id does not exist."
```

2. Not The Diary Owner
```
Status: 401 Unauthorized
Detail: "You do not own this diary."
```

### 4.2. Get Day - `/diary/{diary_id}/{day}` (GET)

**Returns**:

```json
{
  "exercises": "string[]"
}
```

### 4.3. Get Exersise - `/diary/{diary_id}/{day}/{exercise}` (GET)

**Returns**:

```json
{
  "exercise": "string",
  "goal_weight": "int",
  "goal_reps": "int"
}
```

### 4.4. Get ALL Diaries  - `/all` (GET)

**Returns**:

```json
{
  "diary_list": "int[]"
}
```
**Errors**:

1. No Diaries Exist
```
Status: 404 Does Not Exist
Detail: "You have no diaries."
```

### 5. Users

1. `New User`
2. `Login`

### 5.1. Creates New User - `/signup` (POST)

**Request**:

```json
{
  "email": "string",
  "password": "string"
}
```

**Returns**:

```json
{
  "success": "boolean"
}
```

**Errors**:

1. Invalid Email
```
Status: 400 Bad Request
Detail: "Invalid Email"
```

2. Invalid Password Creation
```
Status: 400 Bad Request
Detail: "Password must be at least 8 characters, contain one uppercase letter, and contain one special character."
``` 

3. User Already Registered
```
Status: 409 Conflict
Detail: "User already registered! Login."
```

4. Interal Error
```
Status: 500 Internal Error
Detail: "Something went wrong!"
``` 
### 5.2. Login - `/login` (POST)

**Returns**:

```json
{
  "success": "boolean"
}
```
**Errors**:

1. Wrong Credentials
```
Status: 401 Unauthorized
Detail: "Invalid login credentials. Try again."
```

2. Interal Error
```
Status: 500 Internal Error
Detail: "Something went wrong!"
```
# 6. Exercises

1. `Search exercises`

## 6.1. Search all exercises - `/exercise/search/` (GET)

**Request**:

```query
- exercise (Optional[String]): The exercise parameter filters exercises that contain the specified string (case insensitive). Defaults to no filter.

- sort_order_by_rating (Optional[String]): The direction (of rating) by which the exercises are returned. Defaults to descending. Possible values: "asc" (ascending), "desc" (descending).

- muscle (Optional[String[]]): Filters exercises based on the main muscle worked. Defaults to no filter. Multiple values can be selected from [Chest, Forearms, Lats, Middle Back, Lower Back, Neck, Quadriceps, Hamstrings, Calves, Triceps, Traps, Shoulders, Abdominals, Glutes, Biceps, Adductors, Abductors].

- type (Optional[String[]]): Filters exercises based on the exercise type. Defaults to no filter. Multiple values can be selected from [Cardio, Olympic Weightlifting, Plyometrics, Powerlifting, Strength, Stretching, Strongman].

- equipment (Optional[String[]]): Filters exercises based on the equipment needed. Defaults to no filter. Multiple values can be selected from [Bands, Foam Roll, Barbell, Kettlebells, Body Only, Machine, Cable, Medicine Ball, Dumbbell, None, E-Z Curl Bar, Other, Exercise Ball].

- level (Optional[String[]]): Filters exercises based on the recommended experience level. Defaults to no filter. Multiple values can be selected from [Beginner, Intermediate, Expert].

- count (Optional[String[]]): The maximum number of results returned. Defaults to no maximum.
```

**Returns**:

```json
[
  {
    "name": "string",
    "rating": "float",
    "muscle": "string",
    "type": "string",
    "equipment": "string",
    "level": "string",
    "instructions": "string[]"
  },
  ...
]
```

**Errors**:

1. Diary Does Not Exist

```
Status: 404 Not Found
Detail: "A diary with this id does not exist."
```

2.
