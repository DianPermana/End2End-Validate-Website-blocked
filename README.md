### Evidence Video

https://github.com/user-attachments/assets/efaa671b-8a5d-4c6a-a1cd-fcbe46e13afb


### Daftar 20 Situs MMM yang Diblokir Kominfo, Really ??? 

url : https://www.komdigi.go.id/berita/pengumuman/detail/daftar-20-situs-mmm-yang-diblokir-kominfo


```python
import time
import pandas as pd
import matplotlib.pyplot as plt
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium.common.exceptions import TimeoutException, WebDriverException
from concurrent.futures import ThreadPoolExecutor
```

### Initialization process
    1. Created result dictionary
    2. Created chrome webdriver
    3. Created assertion using title page


```python
def check_website_access(url, expected_result, assert_title=None):
    
    # Initialize the result dictionary
    result = {
        "url": url,
        "expected_result": expected_result,
        "actual_result": "fail",  # Default to fail if something goes wrong
        "timestamp": time.strftime('%Y-%m-%d %H:%M:%S', time.localtime()),  # Will hold the timestamp
        "actual_title": None  # Will hold the actual title of the page
    }

    # Set up the Chrome WebDriver
    driver = webdriver.Chrome()

    try:
        driver.get(url)

        # Capture the actual title of the page
        result["actual_title"] = driver.title
        
        # Assert the page title if specified
        if assert_title:
            try:
                WebDriverWait(driver, 10).until(
                    EC.title_is(assert_title)  # Assert that the page title matches the expected title
                )
                result["actual_result"] = "success"  # Title matches the assertion
            except TimeoutException:
                result["actual_result"] = "fail"  # Title did not match within the timeout period
        else:
            result["actual_result"] = "success"  # If no title to assert, assume the page loaded successfully

    except WebDriverException:
        result["actual_result"] = "fail"  # If the URL failed to load
    finally:
        time.sleep(5)  # Optional sleep time to ensure everything is processed
        driver.quit()  # Close the browser after the check

    return result
```

### Example data : URL, Expected Results and Assert tittle

    1. url : adalah singkatan dari Uniform Resource Locator, yaitu alamat unik yang digunakan untuk mengidentifikasi dan mengakses sumber daya di internet
    2. expected_results : menunjukkan bagaimana seharusnya website (dapat di akses = success, dan tidak dapat diakses = failed )
    3. assert_title : hal paling mudah untuk memastikan bahwa web bisa diakses tentu dengan melihat langsung web nya melalui browser, namun secara teknikal automation kita bisa menggunakan dengan membuat ekspektasi tittle page yang ada pada web tepat setelah muncul ke permukaan, proses ini yang kita manfaatkan untuk melakukan validasi. bahwa optional bisa jadi, tetapi ini hal yang paling basic dapat dilakukan untuk proses validasinya.


```python
# Scenario Outline (Test Cases)
test_data = [
    {"url": "https://www.Indonesia-mmm_net.com", "expected_result": "fail", "assert_title": "The Arabic word ḥarām (حَرَام)"},
    {"url": "https://www.mmmindonesialegal.com", "expected_result": "fail", "assert_title": "The Arabic word ḥarām (حَرَام)"},
    {"url": "https://www.klikmmm.com",           "expected_result": "fail", "assert_title": "The Arabic word ḥarām (حَرَام)"},
    {"url": "https://www.websupportmmm.com",     "expected_result": "fail", "assert_title": "The Arabic word ḥarām (حَرَام)"},
    {"url": "https://www.bisnismavro.com",       "expected_result": "fail", "assert_title": "The Arabic word ḥarām (حَرَام)"},
    {"url": "https://www.mmmindonesiaclub.com",  "expected_result": "fail", "assert_title": "The Arabic word ḥarām (حَرَام)"},
    {"url": "https://www.mmmindonesian.com",     "expected_result": "fail", "assert_title": "The Arabic word ḥarām (حَرَام)"},
    {"url": "https://www.bisnis3m.com",          "expected_result": "fail", "assert_title": "The Arabic word ḥarām (حَرَام)"},
    {"url": "https://www.mmmindonesia1.com",     "expected_result": "fail", "assert_title": "The Arabic word ḥarām (حَرَام)"},
    {"url": "https://www.mmmlovers.com",         "expected_result": "fail", "assert_title": "The Arabic word ḥarām (حَرَام)"},
    {"url": "https://www.mmmindo.com",           "expected_result": "fail", "assert_title": "The Arabic word ḥarām (حَرَام)"},
    {"url": "https://www.lk.sergeymavrodi.com",  "expected_result": "fail", "assert_title": "The Arabic word ḥarām (حَرَام)"},
    {"url": "https://www.lk.sergey-mavrodi-mmm.org","expected_result": "fail", "assert_title": "The Arabic word ḥarām (حَرَام)"},
    {"url": "https://www.mmmcommunity.net",      "expected_result": "fail", "assert_title": "The Arabic word ḥarām (حَرَام)"},
    {"url": "https://www.mmmindonesia9.com",     "expected_result": "fail", "assert_title": "The Arabic word ḥarām (حَرَام)"},
    {"url": "https://www.mmm-dotinfo.com",       "expected_result": "fail", "assert_title": "The Arabic word ḥarām (حَرَام)"},
    {"url": "https://www.mmmincome.com",         "expected_result": "fail", "assert_title": "The Arabic word ḥarām (حَرَام)"},
    {"url": "https://www.2012.sergey-mavrodi.ms","expected_result": "fail", "assert_title": "The Arabic word ḥarām (حَرَام)"},
    {"url": "https://www.2012.sergey-mavrodi-mmm.net","expected_result": "fail", "assert_title": "The Arabic word ḥarām (حَرَام)"},
    {"url": "2012.sergeymavrodi.com",             "expected_result": "fail", "assert_title": "The Arabic word ḥarām (حَرَام)"}
	
	]
```

### Running the tests concurrently using ThreadPoolExecutor
    1. Solusi dari jumlah list website yang di blokir sangat banyak tentu akan memperlambat bagaimana proses pengecekan satu per satu, disini kita mencoba mengimplementasikan parallel execution dengan ThreadPoolExecutor, karena pakai pytest-xdist masih gagal 


```python

with ThreadPoolExecutor(max_workers=20) as executor:
    results = list(
        executor.map(
            lambda data: check_website_access(
                data["url"], 
                data["expected_result"], 
                data.get("assert_title", None)  # Default to None if "assert_title" is missing
            ), 
            test_data
        )
    )
```

### Convert the results to a DataFrame for better display
    1. Get results test
    2. Plot data sukses dan faild menggunakan grapik bar chart
    3. lakukan analisa 


```python
df_results = pd.DataFrame(results)

# Plotting the results (Success vs Fail)
success_count = df_results[df_results["actual_result"] == "success"].shape[0]
fail_count = df_results[df_results["actual_result"] == "fail"].shape[0]

# Plotting a bar chart
plt.figure(figsize=(8, 4))
plt.bar(['Success', 'Fail'], [success_count, fail_count], color=['green', 'red'])
plt.title("Website Access Test Results")
plt.xlabel("Result")
plt.ylabel("Count")
plt.show()

```


    
![png](output_9_0.png)
    


### Display the results as a table (including timestamp and actual title)

Perhatikan matrix pada table berikut

    1. timestamp : memberikan informasi tanggal dan jam berapa website tersebut coba diakses
    2. actual results : fail, menunjukkan 20 website tersebut gagal diakses
    3. actual title : menunjukkan title apa yang terbaca pada website jika berhasil diakses


```python
df_results
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>url</th>
      <th>expected_result</th>
      <th>actual_result</th>
      <th>timestamp</th>
      <th>actual_title</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>https://www.Indonesia-mmm_net.com</td>
      <td>fail</td>
      <td>fail</td>
      <td>2024-11-11 14:24:25</td>
      <td>None</td>
    </tr>
    <tr>
      <th>1</th>
      <td>https://www.mmmindonesialegal.com</td>
      <td>fail</td>
      <td>fail</td>
      <td>2024-11-11 14:24:25</td>
      <td>None</td>
    </tr>
    <tr>
      <th>2</th>
      <td>https://www.klikmmm.com</td>
      <td>fail</td>
      <td>fail</td>
      <td>2024-11-11 14:24:25</td>
      <td>None</td>
    </tr>
    <tr>
      <th>3</th>
      <td>https://www.websupportmmm.com</td>
      <td>fail</td>
      <td>fail</td>
      <td>2024-11-11 14:24:25</td>
      <td>None</td>
    </tr>
    <tr>
      <th>4</th>
      <td>https://www.bisnismavro.com</td>
      <td>fail</td>
      <td>fail</td>
      <td>2024-11-11 14:24:25</td>
      <td>None</td>
    </tr>
    <tr>
      <th>5</th>
      <td>https://www.mmmindonesiaclub.com</td>
      <td>fail</td>
      <td>fail</td>
      <td>2024-11-11 14:24:25</td>
      <td>None</td>
    </tr>
    <tr>
      <th>6</th>
      <td>https://www.mmmindonesian.com</td>
      <td>fail</td>
      <td>fail</td>
      <td>2024-11-11 14:24:25</td>
      <td>None</td>
    </tr>
    <tr>
      <th>7</th>
      <td>https://www.bisnis3m.com</td>
      <td>fail</td>
      <td>fail</td>
      <td>2024-11-11 14:24:25</td>
      <td>None</td>
    </tr>
    <tr>
      <th>8</th>
      <td>https://www.mmmindonesia1.com</td>
      <td>fail</td>
      <td>fail</td>
      <td>2024-11-11 14:24:25</td>
      <td>None</td>
    </tr>
    <tr>
      <th>9</th>
      <td>https://www.mmmlovers.com</td>
      <td>fail</td>
      <td>fail</td>
      <td>2024-11-11 14:24:25</td>
      <td>None</td>
    </tr>
    <tr>
      <th>10</th>
      <td>https://www.mmmindo.com</td>
      <td>fail</td>
      <td>fail</td>
      <td>2024-11-11 14:24:25</td>
      <td>None</td>
    </tr>
    <tr>
      <th>11</th>
      <td>https://www.lk.sergeymavrodi.com</td>
      <td>fail</td>
      <td>fail</td>
      <td>2024-11-11 14:24:25</td>
      <td>None</td>
    </tr>
    <tr>
      <th>12</th>
      <td>https://www.lk.sergey-mavrodi-mmm.org</td>
      <td>fail</td>
      <td>fail</td>
      <td>2024-11-11 14:24:25</td>
      <td>None</td>
    </tr>
    <tr>
      <th>13</th>
      <td>https://www.mmmcommunity.net</td>
      <td>fail</td>
      <td>fail</td>
      <td>2024-11-11 14:24:25</td>
      <td>None</td>
    </tr>
    <tr>
      <th>14</th>
      <td>https://www.mmmindonesia9.com</td>
      <td>fail</td>
      <td>fail</td>
      <td>2024-11-11 14:24:25</td>
      <td>None</td>
    </tr>
    <tr>
      <th>15</th>
      <td>https://www.mmm-dotinfo.com</td>
      <td>fail</td>
      <td>fail</td>
      <td>2024-11-11 14:24:25</td>
      <td>None</td>
    </tr>
    <tr>
      <th>16</th>
      <td>https://www.mmmincome.com</td>
      <td>fail</td>
      <td>fail</td>
      <td>2024-11-11 14:24:25</td>
      <td>None</td>
    </tr>
    <tr>
      <th>17</th>
      <td>https://www.2012.sergey-mavrodi.ms</td>
      <td>fail</td>
      <td>fail</td>
      <td>2024-11-11 14:24:25</td>
      <td>None</td>
    </tr>
    <tr>
      <th>18</th>
      <td>https://www.2012.sergey-mavrodi-mmm.net</td>
      <td>fail</td>
      <td>fail</td>
      <td>2024-11-11 14:24:25</td>
      <td>None</td>
    </tr>
    <tr>
      <th>19</th>
      <td>2012.sergeymavrodi.com</td>
      <td>fail</td>
      <td>fail</td>
      <td>2024-11-11 14:24:25</td>
      <td>None</td>
    </tr>
  </tbody>
</table>
</div>




```python

```
