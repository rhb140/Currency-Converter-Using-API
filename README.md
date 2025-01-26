# Currency Converter Using ExchangeRate-API (Java)

## Description

This project is a **Currency Converter** that fetches real-time exchange rates from the **ExchangeRate-API** and allows users to convert between different currencies using a graphical user interface (GUI). The conversion rates are retrieved dynamically, ensuring accurate and up-to-date results.

## API Source
- **ExchangeRate-API**: [https://www.exchangerate-api.com/](https://www.exchangerate-api.com/)
- **API Documentation**: [https://www.exchangerate-api.com/docs/free](https://www.exchangerate-api.com/docs/free)
- **Note**: You need to sign up for an API key to use the service or you can use the outdated api link. The outdated API link is provided for reference purposes, but it is not recommended for use.

## Libraries Used
The following Java libraries are used in this project:
- **AWT & Swing**: For GUI development
- **HttpURLConnection**: For API requests
- **JSON (org.json)**: To process API responses
- **DecimalFormat**: For formatting numerical output
- **HashMap**: For storing exchange rates efficiently
- **Scanner**: For reading data from API responses

## Maven Configuration

This project uses **Maven** for dependency management and build automation. The `pom.xml` file includes the following key elements:

- **Dependencies**:
  - **org.json**: Used for parsing JSON responses from the ExchangeRate-API.
  - **JUnit**: Included for unit testing (JUnit version `4.11`).

- **Build Configuration**:
  - **Java Compiler Version**: Set to Java 1.11 for compatibility.
  - **Plugins**: Configured plugins for building, cleaning, testing, and packaging the project.

## Code Walkthrough

### Setting Up the GUI
```java
//Create the GUI
JFrame GUI = new JFrame("Currency Converter"); 
GUI.setLayout(new FlowLayout()); // set flowlayout
GUI.setSize(600, 400); // set size
GUI.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE); // set window close on exit
```
- A JFrame window is created for the currency converter
- **FlowLayout** is used to arrange components
- The window size is set to **600x400** pixels

### Getting Exchange Rates
```java
private static void getExchangeRates() throws Exception{
  //url to api
  String url = "https://api.exchangerate-api.com/v4/latest/USD";
  HttpURLConnection APIConnection = (HttpURLConnection) new URI(url).toURL().openConnection();
  APIConnection.setRequestMethod("GET"); // get the information form api
        
  // Read the API
  Scanner apiScanner = new Scanner(APIConnection.getInputStream());
  StringBuilder response = new StringBuilder();

  while (apiScanner.hasNext()){
    response.append(apiScanner.nextLine()); // append each line of the response
  }
  apiScanner.close(); // close scanner

  //get the data from the JSON response and find the exchange rates
  JSONObject jsonResponse = new JSONObject(response.toString());
  JSONObject rates = jsonResponse.getJSONObject("rates");

  // Store extange rates in a hashmap
  for (String key: rates.keySet()) {
    exchangeRates.put(key, rates.getDouble(key));
  }
}
```
- **GET request** is made to the ExchangeRate-API to retrieve currency rates
- **JSON response** is analyzed to extract currency exchange rates
- The rates are stored in a **HashMap** for quick lookup

### Creating UI Components
```java
//add a combobox on the GUI for users to select the input currency 
GUI.add(new JLabel("From (Currency):"));
CurrencyFromInput = new JComboBox<>(exchangeRates.keySet().toArray(new String[0]));
CurrencyFromInput.setEditable(true); // Allow manual entry
GUI.add(CurrencyFromInput);

//add a manual input on the GUI for users to enter value to be converted
GUI.add(new JLabel("Amount"));
amountF = new JTextField(10); //Create a text field for user input
GUI.add(amountF);

//add a combobox on the GUI for users to select the output currency 
GUI.add(new JLabel("To (Currency):"));
CurrencyToInput = new JComboBox<>(exchangeRates.keySet().toArray(new String[0]));
CurrencyToInput.setEditable(true);
GUI.add(CurrencyToInput);

//add a button on GUI to convert currency amount when clicked
JButton convertButton = new JButton("Convert"); // Create a button
convertButton.addActionListener(e -> calculateConversion()); // call calculateConversion() when clicked
GUI.add(convertButton); // Add button to GUI
```
- **JComboBox** is used for currency selection
- **Editable field** allows manual currency entry
- Labels are added to guide the user
- **JTextField** is added for users to input the amount they want to convert
- **JButton** used for "Convert" button to the GUI that triggers the calculateConversion() method when clicked

### Implementing Conversion Logic
```java
private static void calculateConversion() {
    try {
        //get user input
        double amount = Double.parseDouble(amountF.getText()); // Get amount entered by user
        String userCurrency = (String) CurrencyFromInput.getSelectedItem(); // Source currency
        String convertCurrency = (String) CurrencyToInput.getSelectedItem(); // Target currency

        //check if valide user input for the userCurrency and convertCurrency adn return an error message if not
        if (!exchangeRates.containsKey(userCurrency) || !exchangeRates.containsKey(convertCurrency)) {
            resultLabel.setText("Invalid currency selection.");
            return;
        }

        //get the convertion rates based on the user input
        double fromCurrency = exchangeRates.get(userCurrency);
        double toCurrency = exchangeRates.get(convertCurrency);

        // perform conversion calculation
        double convertedValue = (amount / fromCurrency) * toCurrency;
        convertedValue = Math.round(convertedValue * 100.0) / 100.0;

        // format the result
        DecimalFormat formatter = new DecimalFormat("#,###.##");
        String formattedValue = formatter.format(convertedValue);

        // display the result
        resultLabel.setText("Converted Amount: " + formattedValue);
    } catch (NumberFormatException e) {
        resultLabel.setText("Invalid amount. Please enter a number.");
    }
}
```
- Retrieves amount and currency choices from the UI
- Looks up exchange rates in **HashMap**
- Performs the **conversion calculation**
- Uses **DecimalFormat** to format the result
- Handles **input validation errors**

### Displaying Results
```java
resultLabel = new JLabel("Converted Amount: ");
GUI.add(resultLabel);
```
- A **JLabel** is used to display the converted currency amount dynamically

### Running the Application
```java
GUI.setVisible(true);
```
- The JFrame is made visible to launch the application
  
## Screenshot of the GUI

![Currency Converter GUI](https://github.com/rhb140/currency-converter/blob/main/Currency-Converter-GUI.jpg?raw=true)

The graphical user interface of the Currency Converter application showing a converted value based on the user input.

## Conclusion
This currency converter allows users to convert between different currencies using real-time exchange rates. The **GUI interface** makes it user-friendly, and the **real-time API** ensures accuracy.

## Author
Created by **Rory Howe-Borges**  
[rhb140](https://github.com/rhb140)

## Citation
Dataset: ExchangeRate-API. (2025). *Real-Time Exchange Rates*. Retrieved from [https://api.exchangerate-api.com/](https://api.exchangerate-api.com/)

