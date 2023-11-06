# .sh로 코드 실행 스크립트 만들기

아주 간단하게 예를 들어 이러한 code.py가 있다고 해보자. 

```python
def main(feature):
    print(feature)
```

이 코드를 띄워놓은 화면에서 실행하려면 ```main(feature='apple')```이런식으로 하면 된다. 하지만 이 코드를 .sh에서 feature를 지정해 실행하려면 아래와 같이 parser를 할당해야한다.

```python
def main(feature):
    print(feature)
    
    
if __name__ == "__main__":
    parser = argparse.ArgumentParser(description="about feature")
    parser.add_argument("--feature", type=str, help="feature name")

    args = parser.parse_args()
    main(args.feature)
```

.sh 파일에서 변수 이름을 설정해주고, 앞서 설정한 "--feautre" 뒤에 $를 붙여 실행해주면 된다.

```shell
feature="banana"
python main.py --feature $feature
```
