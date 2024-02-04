# Лабораторная 4

## Метод GET /api/question/phrases

- Этот метод возвращает шаблоны фраз и как правильно на них отвечать.
- Параметры запроса: Отсутствуют
- Пример запроса: GET /api/question/phrases
- Пример ответа (в json):
```json
[
    {
        "idZad": 1,
        "phrase": "Plug the scuppers and report",
        "answer": "All csuppers are plugged"
    },
    {
        "idZad": 2,
        "phrase": "Fit bonding wire and report",
        "answer": "Bonding wire us fitted"
    },
    {
        "idZad": 3,
        "phrase": "Stand by absorbent materials and report",
        "answer": "Absorbent materials standing by"
    }
]
```

- В ответе выдается фразы и ответы на них.

- Скрин из постмана:
  Phrases_Headers
  Phrases_BodyAn
- Код теста:
```javascript
pm.test("Response status code is 200", function () {
    pm.expect(pm.response.code).to.equal(200);
});
pm.test("Content-Type header is application/json", function () {
    pm.expect(pm.response.headers.get("Content-Type")).to.include("application/json");
});
pm.test("Validate the structure of the response object", function () {
    const responseData = pm.response.json();
    
    pm.expect(responseData).to.be.an('array');
    responseData.forEach(function(item){
        pm.expect(item).to.be.an('object');
        pm.expect(item.idZad).to.exist.and.to.be.a('number');
        pm.expect(item.phrase).to.exist.and.to.be.a('string');
        pm.expect(item.answer).to.exist.and.to.be.a('string');
    });
});
pm.test("IdZad is a non-negative integer", function () {
    const responseData = pm.response.json();

    pm.expect(responseData).to.be.an('array');
    responseData.forEach(function (item) {
        pm.expect(item.idZad).to.be.a('number').and.to.satisfy((num) => num >= 0, "IdZad should be a non-negative integer");
    });
});
pm.test("Phrase and answer should be non-empty strings", function () {
  const responseData = pm.response.json();
  
  pm.expect(responseData).to.be.an('array');
  responseData.forEach(function(item) {
    pm.expect(item.phrase).to.be.a('string').and.to.have.lengthOf.at.least(1, "Phrase should not be empty");
    pm.expect(item.answer).to.be.a('string').and.to.have.lengthOf.at.least(1, "Answer should not be empty");
  });
});
```

- рез-т тестов:
Phrases_Tests

## Метод GET /api/question/progress/{idUser}

- Этот метод возвращает прогресс пользователя.
- Параметры запроса:
          idUser - индетификатор пользователя
- Пример запроса: GET /api/question/progress/1
- Пример ответа (в json):
```json
{
    "totalQuestions": 100,
    "answerQuestions": 10,
    "persent": 0.1
}
```
В ответе выдается общее кол-во вопросов, кол-во решеных заданий и доля решенных заданий от общего кол-ва
- Скрин из постмана:
  Progress_Headers
  Progress_BodyAn
- Код теста:
  ```javascript
pm.test("Response status code is 200", function () {
    pm.expect(pm.response.code).to.equal(200);
});
pm.test("TotalQuestions is a non-negative integer", function () {
  const responseData = pm.response.json();
  
  pm.expect(responseData).to.be.an('object');
  pm.expect(responseData.totalQuestions).to.be.a('number').and.to.satisfy((value) => value >= 0, "TotalQuestions must be a non-negative integer");
});
pm.test("AnswerQuestions is a non-negative integer", function () {
    const responseData = pm.response.json();
    
    pm.expect(responseData.answerQuestions).to.be.a('number');
    pm.expect(responseData.answerQuestions).to.be.at.least(0);
});
pm.test("Percent is a percentage value between 0 and 1", function () {
  const responseData = pm.response.json();
  
  pm.expect(responseData).to.be.an('object');
  pm.expect(responseData.persent).to.be.a('number');
  pm.expect(responseData.persent).to.be.at.least(0);
  pm.expect(responseData.persent).to.be.at.most(1);
});
  ```

- рез-т тестов:
Progress_Tests

## Метод GET /api/question/id

- Этот метод возвращает задание.
- Параметры запроса:
          id - индетификатор задания
- Пример запроса: GET /api/question/id?id=1
- Пример ответа (в json):
```json
{
    "idZad": 1,
    "phrase": "Plug the scuppers and report",
    "answer": "All csuppers are plugged"
}
```
В ответе выдается фраза и ответ
- Скрин из постмана:
  Question_Headers
  Question_BodyAn
- Код теста:
  ```javascript
pm.test("Response status code is 200", function () {
    pm.response.to.have.status(200);
});
pm.test("Response has the required fields - idZad, phrase, and answer", function () {
  const responseData = pm.response.json();
  
  pm.expect(responseData).to.be.an('object');
  pm.expect(responseData).to.have.property('idZad');
  pm.expect(responseData).to.have.property('phrase');
  pm.expect(responseData).to.have.property('answer');
});
pm.test("IdZad is a non-negative integer", function () {
    const responseData = pm.response.json();
    
    pm.expect(responseData.idZad).to.be.a('number').and.to.satisfy((id) => id >= 0, "IdZad should be a non-negative integer");
});
pm.test("Phrase is a non-empty string", function () {
    const responseData = pm.response.json();
    
    pm.expect(responseData).to.be.an('object');
    pm.expect(responseData.phrase).to.be.a('string').and.to.have.lengthOf.at.least(1, "Phrase should not be empty");
});
pm.test("Content-Type header is application/json", function () {
    pm.expect(pm.response.headers.get("Content-Type")).to.include("application/json");
});
  ```

- рез-т тестов:
Question_Tests

## Метод PUT /api/question/{id}

 Этот метод обновляет информацию о задании.
- Параметры запроса:
   id - идентификатор задания
   phrase - новая фраза (тело запроса)
   answer - новый ответ на фразу (тело запроса)
- Пример запроса: PUT /api/question/1
- Пример тела запроса:
  ```json
    {
      "phrase": "dsg",
      "answer": "sd"
    }
  ```
- Пример ответа:
  ```json
  {
    "id": 1,
    "name": "Updated Well",
    "depth": 1500
  }
  ```
В ответе выдается измененное задание
- Скрин из постмана:
  UpdateQuestion_Headers
  UpdateQuestion_Body

- Код теста:
  ```javascript

pm.test("Response status code is 200", function () {
    pm.expect(pm.response.code).to.equal(200);
});
pm.test("Response has the required fields - idZad, phrase, and answer", function () {
    const responseData = pm.response.json();
    
    pm.expect(responseData).to.be.an('object');
    pm.expect(responseData).to.have.property('idZad');
    pm.expect(responseData).to.have.property('phrase');
    pm.expect(responseData).to.have.property('answer');
});
pm.test("IdZad is a non-negative integer", function () {
  const responseData = pm.response.json();

  pm.expect(responseData).to.be.an('object');
  pm.expect(responseData.idZad).to.be.a('number').and.to.satisfy((num) => num >= 0, "IdZad should be a non-negative integer");
});
pm.test("Phrase and answer must be non-empty strings", function () {
  const responseData = pm.response.json();
  
  pm.expect(responseData).to.be.an('object');
  pm.expect(responseData.phrase).to.be.a('string').and.to.have.lengthOf.at.least(1, "Phrase should not be empty");
  pm.expect(responseData.answer).to.be.a('string').and.to.have.lengthOf.at.least(1, "Answer should not be empty");
});
pm.test("Content-Type header is application/json", function () {
    pm.expect(pm.response.headers.get("Content-Type")).to.include("application/json");
});
  ```

- рез-т тестов:
UpdateQuestion_Tests

## Метод DELETE /api/question/{id}
- Этот метод удаляет задание.
- Параметры запроса:
   id - идентификатор задания

- Пример запроса: DELETE /api/question/1
- Пример ответа:
  ```json
{
    "message": "Задание Удалено"
}
  ```
- Скрин из постмана:
 DeleteQuestion_Headers
 DeleteQuestion_Body
- Код теста:
  ```javascript

pm.test("Response status code is 200", function () {
    pm.expect(pm.response.code).to.equal(200);
});
pm.test("Content-Type header is application/json", function () {
    pm.expect(pm.response.headers.get("Content-Type")).to.include("application/json");
});
pm.test("Response has a message field", function () {
    const responseData = pm.response.json();

    pm.expect(responseData).to.be.an('object');
    pm.expect(responseData.message).to.exist;
});
pm.test("Message field is a non-empty string", function () {
  const responseData = pm.response.json();
  
  pm.expect(responseData).to.be.an('object');
  pm.expect(responseData.message).to.be.a('string');
  pm.expect(responseData.message).to.have.lengthOf.at.least(1, "Message should not be empty");
});
  ```

- рез-т тестов:
DeleteQuestion_Tests


## Метод `POST /api/question`
- Этот метод создает новое задание.
- Параметры запроса:
     phrase - фраза (тело запроса)
     answer - ответ на фразу (тело запроса)
- Пример запроса: POST /api/question
- Пример запроса:
 ```json
{
  "phrase": "ds",
  "answer": "dsdsdsd"
}
```
- Пример ответа:
  ```json
{
    "idZad": 4,
    "phrase": "ds",
    "answer": "dsdsdsd"
}
  ```
- Скрин из постмана:
 PostQuestion_Headers
 PostQuestion_Body
- Код теста:
  ```javascript
pm.test("Response status code is 201", function () {
    pm.expect(pm.response.code).to.equal(201);
});
pm.test("Response has the required fields - idZad, phrase, and answer", function () {
  const responseData = pm.response.json();
  
  pm.expect(responseData).to.be.an('object');
  pm.expect(responseData).to.have.property('idZad');
  pm.expect(responseData).to.have.property('phrase');
  pm.expect(responseData).to.have.property('answer');
});
pm.test("IdZad is a non-negative integer", function () {
    const responseData = pm.response.json();
    
    pm.expect(responseData).to.be.an('object');
    pm.expect(responseData.idZad).to.be.a('number');
    pm.expect(responseData.idZad).to.be.at.least(0);
});
pm.test("Phrase is a non-empty string", function () {
    const responseData = pm.response.json();
    
    pm.expect(responseData).to.be.an('object');
    pm.expect(responseData.phrase).to.be.a('string').and.to.have.lengthOf.at.least(1, "Phrase should not be empty");
});
pm.test("Answer is a non-empty string", function () {
    const responseData = pm.response.json();
    
    pm.expect(responseData.answer).to.be.a('string').and.to.have.lengthOf.at.least(1, "Answer should not be empty");
});
  ```
- рез-т тестов:
PostQuestion_Tests
