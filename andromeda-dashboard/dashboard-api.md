# Dashboard API

The dashboard API can be used by developers to access information on tokens, wallets and more.

## Tokenomics History

Hourly summaries for charting staking and circulating supply over the past 30 days.

{% embed url="https://api.andromedaprotocol.io/v1/tokenomics/history?limit=1" %}

#### Curl

```bash
curl -X GET "https://api.andromedaprotocol.io/v1/tokenomics/history?limit=1"
```

#### Example&#x20;

Get the circluating supply for ANDR token.

{% tabs %}
{% tab title="JavaScript" %}
```javascript
import fetch from 'node-fetch';

// Define the API endpoint
const url = "https://api.andromedaprotocol.io/v1/tokenomics/history?limit=1";

// Fetch data from the API
fetch(url)
  .then(response => {
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }
    return response.json();
  })
  .then(data => {
    // Check if the result array exists and has at least one element
    if (data.result && data.result.length > 0) {
      const circulatingSupply = data.result[0].circulating_supply;
      console.log(circulatingSupply);
    } else {
      console.log('No result data found');
    }
  })
  .catch(error => {
    console.error('Error fetching data:', error);
  });
```
{% endtab %}

{% tab title="Python" %}
```python
import requests

# Define the API endpoint
url = "https://api.andromedaprotocol.io/v1/tokenomics/history?limit=1"

# Fetch data from the API
response = requests.get(url)

# Check if the request was successful
if response.status_code == 200:
    data = response.json()
    # Check if the result array exists and has at least one element
    if 'result' in data and len(data['result']) > 0:
        circulating_supply = data['result'][0]['circulating_supply']
        print(circulating_supply)
    else:
        print('No result data found')
else:
    print(f"Failed to retrieve data: {response.status_code}")
```
{% endtab %}
{% endtabs %}

## Wallets

&#x20;Track known wallets for big activity & enhance security through community monitoring.

{% embed url="https://api.andromedaprotocol.io/v1/tokenomics/wallets" %}

#### Curl

```bash
curl -X GET "https://api.andromedaprotocol.io/v1/tokenomics/wallets"
```

### Example

Get the top 5 wallets with most ANDR tokens.

{% tabs %}
{% tab title="JavaScript" %}
```javascript
import fetch from 'node-fetch';

// Define the API endpoint
const url = "https://api.andromedaprotocol.io/v1/tokenomics/wallets";

// Fetch data from the API
fetch(url)
  .then(response => {
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }
    return response.json();
  })
  .then(data => {
    // Check if the result array exists and has at least one element
    if (data.result && data.result.length > 0) {
      // Sort wallets by balance in descending order and get the top 5
      const topHolders = data.result.sort((a, b) => b.balance - a.balance).slice(0, 5);
      topHolders.forEach((holder, index) => {
        console.log(`${index + 1}. Address: ${holder.address}, Balance: ${holder.balance}`);
      });
    } else {
      console.log('No wallets data found');
    }
  })
  .catch(error => {
    console.error('Error fetching data:', error);
  });
```
{% endtab %}

{% tab title="Python" %}
```python
import requests

# Define the API endpoint
url = "https://api.andromedaprotocol.io/v1/tokenomics/wallets"

# Fetch data from the API
response = requests.get(url)

# Check if the request was successful
if response.status_code == 200:
    data = response.json()
    # Check if the result array exists and has at least one element
    if 'result' in data and len(data['result']) > 0:
        top_holders = sorted(data['result'], key=lambda x: x['balance'], reverse=True)[:5]
        for i, holder in enumerate(top_holders, 1):
            print(f"{i}. Address: {holder['address']}, Balance: {holder['balance']}")
    else:
        print('No wallets data found')
else:
    print(f"Failed to retrieve data: {response.status_code}")
```
{% endtab %}
{% endtabs %}

## Vesting Emissions

Get real-time vesting emission rates & project token releases for the next 2 years.

{% hint style="warning" %}
You can define the timestamp to get the emission rate for at the end of the URL as seen below.
{% endhint %}

{% embed url="https://api.andromedaprotocol.io/v1/tokenomics/vesting-emission?timestamp=1721055818196" %}

#### Curl

```bash
curl -X GET "https://api.andromedaprotocol.io/v1/tokenomics/vesting-emission?timestamp=1721055818196"
```

#### Example

Get vesting emission per second for the timestamp 1731055818196.

{% tabs %}
{% tab title="JavaScript" %}
```javascript
import fetch from 'node-fetch';

// Define the API endpoint with the timestamp
const url = "https://api.andromedaprotocol.io/v1/tokenomics/vesting-emission?timestamp=1731055818196";

// Fetch data from the API
fetch(url)
  .then(response => {
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }
    return response.json();
  })
  .then(data => {
    // Check if 'vesting_emission_per_second' key exists in the data
    if (data.vesting_emission_per_second) {
      const vestingEmissionPerSecond = data.vesting_emission_per_second;
      console.log(`Vesting Emission Per Second: ${vestingEmissionPerSecond}`);
    } else {
      console.log('No vesting emission per second data found');
    }
  })
  .catch(error => {
    console.error('Error fetching data:', error);
  });
```
{% endtab %}

{% tab title="Python" %}
```python
import requests

# Define the API endpoint with the timestamp
url = "https://api.andromedaprotocol.io/v1/tokenomics/vesting-emission?timestamp=1731055818196"

# Fetch data from the API
response = requests.get(url)

# Check if the request was successful
if response.status_code == 200:
    data = response.json()
    # Check if 'vesting_emission_per_second' key exists in the data
    if 'vesting_emission_per_second' in data:
        vesting_emission_per_second = data['vesting_emission_per_second']
        print(f"Vesting Emission Per Second: {vesting_emission_per_second}")
    else:
        print('No vesting emission per second data found')
else:
    print(f"Failed to retrieve data: {response.status_code}")
```
{% endtab %}
{% endtabs %}
