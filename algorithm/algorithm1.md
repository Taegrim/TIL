# 알고리즘 함수1
+ 최대공약수(GCD), 최소공배수(LCM)와 유클리드 호제법
+ stoi를 활용한 진법 변환
+ next_permutation (순열과 조합)
+ sstream을 이용한 파싱
#
<br>


+ ### 최대공약수(GCD), 최소공배수(LCM)와 유클리드 호제법
C++17 부턴 최대공약수, 최소공배수 함수가 구현되어있다
```
#include <iostream>
#include <numeric> // gcd, lcm 사용을 위한 헤더

int main() {
    int a = 12, b = 18;
    std::cout << "GCD: " << std::gcd(a, b) << std::endl; // 6
    std::cout << "LCM: " << std::lcm(a, b) << std::endl; // 36
}
```
위와같이 numeric 헤더를 추가하고 gcd, lcm 함수를 호출하면 간단하게 최대공약수와 최소공배수를 구할 수 있다
<br>

```
int GCD(int a, int b) {
    while (b != 0) {
        int r = a % b;
        a = b;
        b = r;
    }
    return a;
}

int LCM(int a, int b) {
    return (a * b) / GCD(a, b);
}
```
C++17 미만 환경에선 위와 같이 while문으로 GCD, LCM을 직접 구현하면 된다
<br><br><br><Br>

+ ### stoi를 활용한 진법 변환
stoi(const std::string& str, std::size_t* pos = 0, int base = 10)<br>
stoi 함수의 원형은 위와 같다<br>
첫번째 인자로 string을 받고, 두번째 인자로 몇개의 문자를 읽었는지를 저장할 포인터 위치, 세번째 인자로 진법의 종류를 받는다<br>


```
#include <string>

int solution(int n) {
    std::string s{""};
    
    while(n > 0) {
        s += std::to_string(n % 3);
        n /= 3;
    }
    
    int answer = std::stoi(s, nullptr, 3);
    return answer;
}
```
위의 코드는 정수를 받아서 3진법으로 만들고 이를 뒤집은다음 다시 10진법으로 변환하는 함수이다<br>
while문으로 3진법을 변환할 때 다른 동작을 취하지 않으면 기본적으로 뒤집혀 있어서 이대로 stoi를 사용하면 뒤집힌 3진법을 10진법으로 바꿀 수 있다
<br><br><br><Br>

+ ### next_permutation (순열과 조합)
컨테이너에서 순열과 조합을 구하는데 next_permutation 함수를 이용하면 쉽게 구할 수 있다<br>
next_permutaion 은 사전순으로 봤을 때 다음 순서가 있다면 원소의 자리를 바꾸고 true를 반환한다<br>
만약 다음순서가 없는 마지막 순서에 도달하면 false를 반환한다<br>
사전순서대로 진행하기 때문에 모든 경우를 확인하려면 오름차순 정렬이 필요하다<br><br>

nCm 조합의 경우엔 N개짜리 배열을 만들고 원소를M개만큼 마스킹 하면 된다<br>
next_permutation은 중복을 알아서 걸러주기 때문에 flagbit 처럼 사용할 배열을 이용하면 쉽게 조합을 구할 수 있다<br>

```
#include <string>
#include <vector>
#include <algorithm>

int solution(std::vector<int> number) {
    int answer = 0;
    int size = number.size();
    
    std::vector<int> flag(size, 0);
    std::fill(flag.end() - 3, flag.end(), 1);

    do {
        int sum = 0;

        for (int i = 0; i < size; ++i) {
            if (flag[i] == 1) {
                sum += number[i];
            }
        }

        if (sum == 0) {
            ++answer;
        }

    } while (std::next_permutation(flag.begin(), flag.end()));

    return answer;
}
```
위의 코드는 number 정수 벡터에서 원소 3개의 합이 0인 조합 갯수를 반환하는 함수이다<br>
우선 number를 오름차순으로 정렬하여 next_permutation을 처리할 준비를 한다<br>
조합으로 처리하기 위해 flag 벡터를 선언하고 뒤에서부터 3개를 1로 채워준다<br>
next_permutation에서 모양을 바꿀 컨테이너는 flag이기 때문에 flag를 오름차순 형태로 만들어주는 것이다<br>
이후에 do-while을 이용해서 조합을 구하면 된다
<br><br><br><br>



+ ### sstream을 이용한 파싱
문자열에서 문자들을 공백문자와 \n 을 기준으로 끊어 읽을 수 있다<br>
공백문자의 개수와 무관하게 하나로 묶어서 처리한다<br>
sstream 헤더를 추가해서 사용한다<br>
사용 방법은 stringstream >> string 이고 cin 과 유사하게 사용한다<br>

```
#include <string>
#include <vector>
#include <sstream>
#include <unordered_map>

using namespace std;

vector<string> solution(vector<string> record) {
    vector<string> answer;
    unordered_map<string, string> m;

    for (const string& s : record) {
        stringstream ss(s);
        string event, uid, name;

        ss >> event >> uid;

        if (event == "Enter" || event == "Change") {
            ss >> name;
            m[uid] = name;
        }
    }

    for (const string& s : record) {
        stringstream ss(s);
        string event, uid;

        ss >> event >> uid;

        if (event == "Enter") {
            answer.push_back(m[uid] + "님이 들어왔습니다.");
        }
        else if (event == "Leave") {
            answer.push_back(m[uid] + "님이 나갔습니다.");
        }
    }

    return answer;
}
```
위의 코드는 프로그래머스 '오픈채팅방'을 해결한 코드로 채팅방의 로그를 받아서 사용자에게 최종적으로 보여주는 메세지를 반환하는 함수이다<br><br>

["Enter uid1234 Muzi", "Enter uid4567 Prodo","Leave uid1234","Enter uid1234 Prodo","Change uid4567 Ryan"]<br>
와 같이 "명령 uid (이름)" 으로 구성된 문자열들을 받아서 처리해야 한다<br>

name은 Enter, Change시에 변할 수 있고 마지막에 변한 값으로 전부 바꿔야 하기 때문에 unordered_map을 사용했다<br>
왜냐하면 map, unordered_map은 map[key] = value 형태로 작성하면 가장 마지막에 실행한 형태로 value 값이 저장되기 때문이다<Br>
stringstream ss를 선언하고 문자열에서 명령, uid 를 추출하고 Enter,Change의 경우엔 name까지 추출하여 unordered_map에 저장한다<br>
이후엔 다시 for문을 돌려 Enter,Leave의 경우에 사용자에게 보일 문구를 담아주면 된다

