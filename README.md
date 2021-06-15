# Introduction
This library provides a pure `C#` interface for working with the [API](https://en.wikipedia.org/wiki/API) of popular virtual number services.  
In addition to the pure API implementation, this library features a number of high-level classes to make the development easy and straightforward.

# Installation
### Installing via NuGet
The easiest way to install **sms-services-kit** is via `NuGet`.
In Visual Studio's [Package Manager Console](http://docs.nuget.org/docs/start-here/using-the-package-manager-console), enter the following command:
```
Install-Package sms-services-lib
```

### Getting the Source Code
You'll need to clone **sms-service-kit** from my GitHub repository. To do this using the command-line version of Git, you'll need to issue the following command in your terminal:
```
git clone --recursive https://github.com/rattzv/sms-service-kit.git
```

# Getting started
In the chapter [Documentation](#Documentation) you will find a description of all services and their methods. This example uses the `sms-activate.ru` service:
### Client initialization:

```csharp
SMSActivateCore client = SMSActivate.InitializeClient("api-key");
```
>`api-key` - required input parameter.  

Further work with the library takes place through the `client`.

# Documentation

### SMS-ACTIVATE.RU
Homepage service - [sms-activate.ru](https://sms-activate.ru/?ref=131777)
- #### Methods  
- **GetBalance()** - Available balance request
    ```csharp
    SMSActivateCore client = SMSActivate.InitializeClient("api-key");
    double balance = client.GetBalance();
    ```
- **GetNumbersCount()** - Available countries and services

  All available countries and services/products are listed in enum. You just need to select the desired country and service from the list and pass it to the method, like this:
  ```csharp
  Country countryName = Country.Russia;
  Service serviceName = Service.Telegram;
  string CountNum = client.GetNumbersCount(serviceName, countryName)
  ```
  > `countryName` - required input parameter.  
    `serviceName` - required input parameter.

  However, this is not the only use case. You can also pass string parameters to the method, in accordance with the accepted on the site:
  ```csharp
  string numbersCount = client.GetNumbersCount("0", "tg");
  ```

- **GetNumber()** - Purchase a virtual number

  To get the number, select the desired service and country from the enum, as in the method **GetNumbersCount**
  ```csharp
  Country countryName = Country.Russia;
  Service serviceName = Service.Telegram;
  Number GetNum = client.GetNumber(serviceName, countryName)
  ```  

    However, this is not the only use case. You can also pass string parameters to the method, in accordance with the accepted on the site:
  ```csharp
  Number GetNum = client.GetNumber("0", "tg");
  ```

  You can also use an additional parameter `wait` to wait for phone numbers to appear on the SMS service

  ```csharp
  bool wait = true;
  int waitTime = 10;
  Number GetNum = client.GetNumber(Service.Telegram, Country.Russia, wait, waitTime)//Waiting for a number within 10 seconds
  ```

  Now to get the activation id, you must use the following "id field" and "phone field" respectively

  ```csharp
  string id = GetNum.id;
  string phone_number = GetNum.phone;
  ```

- **SetActivationStatus()** - Use it to set the activation status
  
  ```csharp
  client.SetActivationStatus(ActivationStatus.ReadyForSms, GetNum.id);
  ```
  The possible statuses to change the activation:
  > `DenyActivation`, `FinishActivation`, `ReadyForSms`, `RequestCodeAgain`

- **GetActivationStatus()** - Use it to get the code

  ```csharp
  string code = client.GetActivationStatus(GetNum.id);
  ```
  You can use additional parameters `wait` to wait for the code for a specified period of time
 
  Possible responses returned:
  > `STATUS_WAIT_CODE`, `STATUS_WAIT_RESEND`, `STATUS_CANCEL`, `STATUS_WAIT_RETRY`


- **Exceptions**
  - **BadKeyErrorException**  
    The exception that is thrown when the `API-Key` is entered incorrectly.
    ```csharp
    try
    {
      SMSActivateCore client = SMSActivate.InitializeClient("invalidAPIKey");
    }
    catch(BadKeyErrorException)
    {
      Console.Writeline("Invalid API-Key");
    }
    ```


<details>
<summary><strong>BadActionErrorException</strong></summary>

  The exception that occurs when an invalid action is performed
</details>
<details>
<summary><strong>BadServicesErrorExeption</strong></summary>

  The exception that incorrect name of the service
  ```csharp
    try
    {
      Number GetNum = client.GetNumber("telegram", "0", wait, 2000);
    }
    catch(BadServicesErrorExeption)
    {
      Console.Writeline("Incorrect name of the service");
    }
  ```  
</details>
<details>
<summary><strong>BadStatusErrorException</strong></summary>

  The Exception when the activation status is incorrect

  ```csharp
    try
    {
      client.SetActivationStatus(ActivationStatus.incorrectStatus);
    }
    catch(BadStatusErrorException)
    {
      Console.Writeline("Incorrect activation status");
    }
  ```  
</details>
<details>
<summary><strong>BannedAccountErrorExeption</strong></summary>

An exception occurs when the account is banned

  ```csharp
    try
    {
      Number GetNum = client.GetNumber(serviceName, countryName)
    }
    catch(BannedAccountErrorExeption)
    {
      Console.Writeline("BANNED");
    }
  ```  
</details>
<details>
<summary><strong>NoActivationErrorException</strong></summary>

An exception occurs when the activation id does not exist

  ```csharp
    try
    {
      client.SetActivationStatus(ActivationStatus.ReadyForSms, GetNum.id);
    }
    catch(NoActivationErrorException)
    {
      Console.Writeline("Activation id does not exist");
    }
  ```  
</details>
<details>
<summary><strong>NoBalanceErrorExeption</strong></summary>

This exception indicates a zero balance

  ```csharp
    try
    {
      Number GetNum = client.GetNumber(serviceName, countryName)
    }
    catch(NoBalanceErrorExeption)
    {
      Console.Writeline("Balance ended");
    }
  ```  
</details>
<details>
<summary><strong>NoNumbersErrorExeption</strong></summary>

An exception that occurs when there are no numbers on the service

  ```csharp
    try
    {
      Number GetNum = client.GetNumber(serviceName, countryName)
    }
    catch(NoNumbersErrorExeption)
    {
      Console.Writeline("NO NUMBERS");
    }
  ```  
</details>
<details>
<summary><strong>SQLServerErrorException</strong></summary>

Exception that occurs when an SQL server error occurs

  ```csharp
    try
    {
      double balance = client.GetBalance();
    }
    catch(SQLServerErrorException)
    {
      Console.Writeline("ERROR SQL");
    }
  ```  
</details>

# Donate
# Reporting Bugs
# Contacts
# The MIT License

Copyright © `2020` `Rattz Vadim`

Permission is hereby granted, free of charge, to any person
obtaining a copy of this software and associated documentation
files (the “Software”), to deal in the Software without
restriction, including without limitation the rights to use,
copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the
Software is furnished to do so, subject to the following
conditions:

The above copyright notice and this permission notice shall be
included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED “AS IS”, WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES
OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND
NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT
HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY,
WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING
FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR
OTHER DEALINGS IN THE SOFTWARE.
