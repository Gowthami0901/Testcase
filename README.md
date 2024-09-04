# Testcases

**Overview**  
Test cases are essential components of the software development lifecycle that verify the functionality of code. They help ensure that the code behaves as expected in various scenarios, including edge cases and error conditions. By writing and running test cases, you can catch bugs early, ensure code quality, and facilitate future code changes with confidence.

A test case is a set of conditions or variables used to determine if a software application or system behaves as expected. It typically includes the following components:

1. **Test Case ID**: A unique identifier for the test case.
2. **Test Case Description**: A brief description of what the test case is intended to verify.
3. **Preconditions**: Any conditions or setup required before the test case can be executed.
4. **Test Steps**: The specific steps or actions to be performed during the test.
5. **Test Data**: Any input data required to execute the test.
6. **Expected Result**: The expected outcome of the test if everything is functioning correctly.
7. **Actual Result**: The actual outcome observed when the test is executed.
8. **Status**: Whether the test case passed or failed based on the comparison between the expected and actual results.
<br>

## Why Test Cases Are Needed

1. **Validation**: Ensure the code performs the required functions correctly.
2. **Bug Detection**: Identify issues before the software is deployed.
3. **Code Changes**: Facilitate refactoring and adding new features without breaking existing functionality.
4. **Documentation**: Provide a reference for how the code is supposed to behave.
<br>

## Detailed Reasons for Test Cases

1. **Verify Functionality**: Test cases help ensure that the software behaves as expected and meets its requirements. They validate that features work correctly and that changes or new features don't introduce bugs.

2. **Catch Bugs Early**: By running test cases, you can identify and fix issues early in the development process. This helps prevent problems from making it into production.

3. **Ensure Quality**: Well-designed test cases contribute to the overall quality of the software. They help maintain high standards and ensure that the software is reliable and performs well.

4. **Facilitate Communication**: Test cases provide a clear and detailed description of what needs to be tested. They help communicate expectations between developers, testers, and stakeholders.

5. **Support Regression Testing**: As software evolves, test cases can be re-run to ensure that existing functionality is not broken by new changes. This is known as regression testing.

6. **Documentation**: Test cases serve as documentation of the expected behavior of the software. They provide a reference for future development, maintenance, and troubleshooting.

7. **Automation**: Test cases can be automated using testing frameworks and tools. Automated tests can be run frequently and consistently, reducing manual testing effort and improving efficiency.

8. **Improves Reliability**: Systematic testing with well-defined test cases helps in improving the reliability of the software by catching edge cases and ensuring consistent performance.
<br>

## Test Case Components

1. **Test Case ID**: A unique identifier for the test case.
2. **Title/Description**: A concise description of the purpose of the test case.
3. **Test Objective**: The specific goal or objective of the test.
4. **Preconditions**: Any necessary conditions that must be met before the test is executed.
5. **Test Steps**: A step-by-step sequence of actions to perform during the test.
6. **Input Data**: The data or parameters to be used as input for the test.
7. **Expected Results**: The anticipated outcomes or behaviors after executing the test steps.
8. **Actual Results**: The actual outcomes observed when executing the test.
9. **Test Environment**: Details about the system, hardware, software, and configurations used for testing.
10. **Test Data Preparation**: Instructions on how to set up the required test data.
11. **Test Execution Date**: The date and time when the test was executed.
12. **Test Execution Status**: The pass/fail status of the test case after execution.
13. **Test Conclusion**: A summary of the results and observations of the test.
14. **Test Verdict**: A judgment about the overall success of the test.
15. **Bug Details (optional)**: If a defect is identified, details about the issue, its severity, and steps to reproduce it. Typically managed in a separate dashboard but can be included here if needed.
16. **Attachments**: Any relevant files, screenshots, or documentation associated with the test.
17. **Test Case Author**: The person responsible for creating the test case.
18. **Test Case Reviewer**: The person who reviewed and approved the test case.
19. **Test Case Version**: The version or revision number of the test case.
20. **Notes/Comments**: Additional information, insights, or comments related to the test case.
<br>

## Test Case vs. Test Scenario

**Test Case** and **Test Scenario** are related but distinct concepts in software testing:

| **Aspect**            | **Test Case**                                    | **Test Scenario**                               |
|-----------------------|--------------------------------------------------|-------------------------------------------------|
| **Definition**        | Specific set of steps and expected results      | High-level description of functionality          |
| **Focus**             | Tests a single aspect or condition              | Encompasses multiple related test cases         |
| **Identification**    | Has a unique identifier (Test Case ID)          | Often identified by a descriptive title          |
| **Purpose**           | Detailed verification of a small unit           | Provides context for a group of test cases      |
| **Granularity**       | Very specific and focused                       | More general and covers broader functionality   |
| **Contents**          | Test steps, inputs, expected results, etc.      | High-level description, related test cases      |
| **Reusability**       | May be reused in different scenarios            | Often used as a reference for grouping          |
| **Coverage**          | Addresses one specific behavior/scenario        | Covers multiple test scenarios                  |
| **Example**           | Testing user login with invalid password        | Testing the user registration process           |



By understanding these components and distinctions, you can effectively design and manage your test cases and scenarios, ensuring thorough and efficient software testing.


**Test Cases Explanation**

Below is a detailed explanation of the test cases implemented using `pytest` and `FlaskClient` for a Flask application that handles incoming messages and saves chat history:

1. **`test_verify_webhook`**
   - **Purpose**: Verify that the `/webhook` endpoint returns the expected challenge response for a valid request.
   - **Details**:
     - Sends a GET request to the `/webhook` endpoint with the `hub.mode`, `hub.verify_token`, and `hub.challenge` parameters.
     - Asserts that the response status code is `200` and the response data matches the `hub.challenge`.

   ```python
   def test_verify_webhook(client: FlaskClient):
       response = client.get('/webhook', query_string={
           'hub.mode': 'subscribe',
           'hub.verify_token': 'hrrecruiter',
           'hub.challenge': 'test_challenge'
       })
       assert response.status_code == 200
       assert response.data.decode() == 'test_challenge'
   ```

2. **`test_verify_webhook_invalid_token`**
   - **Purpose**: Ensure the `/webhook` endpoint returns a `403 Forbidden` status for an invalid verification token.
   - **Details**:
     - Sends a GET request with an incorrect `hub.verify_token`.
     - Asserts that the response status code is `403` and the response data indicates a verification token mismatch.

   ```python
   def test_verify_webhook_invalid_token(client: FlaskClient):
       response = client.get('/webhook', query_string={
           'hub.mode': 'subscribe',
           'hub.verify_token': 'invalid_token',
           'hub.challenge': 'test_challenge'
       })
       assert response.status_code == 403
       assert response.data.decode() == 'Verification token mismatch'
   ```

3. **`test_webhook_post`**
   - **Purpose**: Test that the `/webhook` endpoint processes incoming messages correctly.
   - **Details**:
     - Mocks the `process_incoming_message` function to verify that it is called with the correct data.
     - Sends a POST request with a mock message ID.
     - Asserts that the response status code is `200` and `process_incoming_message` is called with the mock data.

   ```python
   @patch('main.process_incoming_message')
   def test_webhook_post(mock_process_incoming_message, client: FlaskClient):
       message_id = str(uuid.uuid4())
       mock_data = {"entry": [{"changes": [{"value": {"messages": [{"id": message_id}]}}]}]}
       response = client.post('/webhook', json=mock_data)
       assert response.status_code == 200
       mock_process_incoming_message.assert_called_once_with(mock_data)
   ```

4. **`test_save_chat_history_insert`**
   - **Purpose**: Verify that `save_chat_history` correctly inserts a new chat record into the database.
   - **Details**:
     - Mocks the `chats_collection` to simulate the database interactions.
     - Sets up a scenario where no existing chat history is found.
     - Asserts that `insert_one` is called with the correct data including `chat_id`, `phone_number`, and `history`.

   ```python
   @patch('main.chats_collection')
   def test_save_chat_history_insert(mock_chats_collection):
       message = get_random_interview_message()
       user_message = message['user_message']
       bot_message = message['bot_message']
       phone_number = str(uuid.uuid4().int)[:10]
       current_date = datetime.now().strftime('%Y-%m-%d')

       mock_chats_collection.find_one.return_value = None

       save_chat_history(phone_number, user_message, bot_message)
       
       mock_chats_collection.insert_one.assert_called_once()
       call_args = mock_chats_collection.insert_one.call_args[0][0]
       assert call_args['chat_id'] == f'chat_{phone_number}'
       assert call_args['phone_number'] == phone_number
       assert call_args['history'][current_date][0]['user'] == user_message
       assert call_args['history'][current_date][0]['bot'] == bot_message
   ```

5. **`test_save_chat_history_update`**
   - **Purpose**: Verify that `save_chat_history` correctly updates an existing chat record.
   - **Details**:
     - Mocks the `chats_collection` to simulate finding an existing chat history.
     - Asserts that `update_one` is called to update the existing history with the new chat details.

   ```python
   @patch('main.chats_collection')
   def test_save_chat_history_update(mock_chats_collection):
       message = get_random_interview_message()
       user_message = message['user_message']
       bot_message = message['bot_message']
       phone_number = str(uuid.uuid4().int)[:10]
       current_date = datetime.now().strftime('%Y-%m-%d')

       existing_chat = {
           'chat_id': f'chat_{phone_number}',
           'history': {
               current_date: [{'user': 'Hello', 'bot': 'Hi there!'}]
           }
       }
       mock_chats_collection.find_one.return_value = existing_chat

       save_chat_history(phone_number, user_message, bot_message)
       
       mock_chats_collection.update_one.assert_called_once()
       call_args = mock_chats_collection.update_one.call_args[0][1]['$set']['history'][current_date]
       assert len(call_args) == 2
       assert call_args[1]['user'] == user_message
       assert call_args[1]['bot'] == bot_message
   ```

6. **`test_webhook_post_invalid_json`**
   - **Purpose**: Verify that the `/webhook` endpoint returns a `400 Bad Request` for invalid JSON data.
   - **Details**:
     - Sends a POST request with invalid JSON data.
     - Asserts that the response status code is `400` and `process_incoming_message` is not called.

   ```python
   @patch('main.process_incoming_message')
   def test_webhook_post_invalid_json(mock_process_incoming_message, client: FlaskClient):
       mock_data = "invalid_json"
       response = client.post('/webhook', data=mock_data, content_type='application/json')
       assert response.status_code == 400
       mock_process_incoming_message.assert_not_called()
   ```

7. **`test_save_chat_history_complex_nested_structure`**
   - **Purpose**: Verify that `save_chat_history` handles complex nested chat history structures.
   - **Details**:
     - Mocks the `chats_collection` to simulate a chat history with a complex nested structure.
     - Asserts that `update_one` correctly appends the new chat messages to the existing history.

   ```python
   @patch('main.chats_collection')
   def test_save_chat_history_complex_nested_structure(mock_chats_collection):
       user_message = "Tell me more about the benefits."
       bot_message = "We offer healthcare, 401k, and more."
       phone_number = str(uuid.uuid4().int)[:10]
       current_date = datetime.now().strftime('%Y-%m-%d')
       
       existing_chat = {
           'chat_id': f'chat_{phone_number}',
           'history': {
               current_date: [
                   {
                       'user': 'Hi',
                       'bot': 'Hello!'
                   },
                   {
                       'user': 'What are the company values?',
                       'bot': 'Integrity, Innovation, and Excellence.'
                   }
               ]
           }
       }
       mock_chats_collection.find_one.return_value = existing_chat
       
       save_chat_history(phone_number, user_message, bot_message)
       
       mock_chats_collection.update_one.assert_called_once()
       call_args = mock_chats_collection.update_one.call_args[0][1]['$set']['history'][current_date]
       assert len(call_args) == 3
       assert call_args[2]['user'] == user_message
       assert call_args[2]['bot'] == bot_message
   ```

8. **`test_webhook_post_with_additional_unrelated_fields`**
   - **Purpose**: Verify that the `/webhook` endpoint processes valid messages correctly even when additional unrelated fields are present.
   - **Details**:
     - Sends a POST request with additional fields in the message data.
     - Asserts that `process_incoming_message` is called with the correct data.

   ```python
   @patch('main.process_incoming_message')
   def test_webhook_post_with_additional_unrelated_fields(mock_process_incoming_message, client: FlaskClient):
       message_id = str(uuid.uuid4())
       mock_data = {
           "entry": [{
               "changes": [{
                   "value": {
                       "messages": [{"id": message_id}],
                       "unrelated_field": "some_value"
                   }
               }]
           }]
       }
       response = client.post('/webhook', json=mock_data)
       assert response.status_code == 200
       mock_process_incoming_message.assert_called_once

_with(mock_data)
   ```

9. **`test_webhook_post_unsupported_content_type`**
   - **Purpose**: Verify that the `/webhook` endpoint returns a `415 Unsupported Media Type` status for unsupported content types.
   - **Details**:
     - Sends a POST request with XML content type.
     - Asserts that the response status code is `415` and `process_incoming_message` is not called.

   ```python
   @patch('main.process_incoming_message')
   def test_webhook_post_unsupported_content_type(mock_process_incoming_message, client: FlaskClient):
       mock_data = "<xml><data>Invalid</data></xml>"
       response = client.post('/webhook', data=mock_data, content_type='application/xml')
       assert response.status_code == 415
       mock_process_incoming_message.assert_not_called()
   ```

**Conclusion**  
These test cases cover various scenarios for verifying the behavior of a Flask application handling webhooks and saving chat history. They test both successful operations and edge cases, such as invalid input or unsupported content types, ensuring the robustness and reliability of the application.
