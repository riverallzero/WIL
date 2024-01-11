# 웹브라우저가 로딩될때까지 기다리기

셀레니움으로 웹에서 로그인과 같이 키를 보내고 동작하거나 다른 페이지를 로딩할때 시간이 걸릴때가 있다. 
이때 찾고자하는 객체가 로딩되지 않았다면 코드는 동작하지 않고 오류로 멈추게된다.
따라서 요소가 웹페이지에 나올때까지 로딩을 기다린 뒤 작업을 처리해야한다.
이를 적용한 네이버에서 로그인하는 예시로 ```자동입력문자방지```는 코드에 포함하지않아 실제 로그인이 되지는 않는다는 것을 주의하자.

```python
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC

id = 'naver_id'
password = 'naver_pw'

driver = webdriver.Chrome()
url = 'https://nid.naver.com/nidlogin.login?mode=form&url=https://www.naver.com/'
driver.get(url)


def wait_and_find_element(driver, by, value, timeout=10):
    return WebDriverWait(driver, timeout).until(
        EC.presence_of_element_located((by, value))
    )

# 아이디 입력
id_input = wait_and_find_element(driver, By.XPATH, '//*[@id="id"]')
id_input.send_keys(id)

# 비밀번호 입력
pw_input = wait_and_find_element(driver, By.XPATH, '//*[@id="pw"]')
pw_input.send_keys(password)

# 로그인
login_button = wait_and_find_element(driver, By.XPATH, '//*[@id="log.login"]')
login_button.click()
```
