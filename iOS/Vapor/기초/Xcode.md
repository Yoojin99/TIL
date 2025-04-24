# Xcode

Xcode 를 사용한 팁 / 트릭

## Custom Working Directory

기본적으로 Xcode 는 *DerivedData* 폴더 에서 프로젝트를 실행함. 이 폴더는 프로젝트의 root folder(`Package.swift` 파일이 있는 곳) 과는 다름.

즉 Vapor 가 *.env*, *Public* 같은 파일/폴더를 찾을 수 없다는 뜻.

앱 실행 중 아래와 같은 warning 이 뜨면 vapor 가 이런 파일 / 폴더를 찾을 수 없다는 뜻.

```
[ WARNING ] No custom working directory set for this scheme, using ...
```

이를 해결하기 위해서 Xcode scheme 에 custom working directory 를 설정해야 함.

스킴을 선탠한 후 *Edit Scheme* 선택

<img width="234" alt="image" src="https://github.com/user-attachments/assets/6a239da5-cf93-43ac-890d-ad5008a33239">

Run > Options > Working Directory 에 프로젝트의 root folder 경로 입력

<img width="946" alt="image" src="https://github.com/user-attachments/assets/b2e35dd8-8c88-4654-81f8-f56566a27aff">

다시 어플리케이션을 실행하면 warning 안뜸

