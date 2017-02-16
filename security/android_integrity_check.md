# What
Android App. integrity check 

# How
## App Integrity Check Method
App integriticy check는 NDK를 이용하여 native 에서 구현

    (In App.)
    get certificate of APK
    for all entry in APK
      if !compare(entry.cert, cert) 
        return error 
    hash = Base64(SHA-1(cert))
    send APK info(hash, APK version) to server
    
    (In Server)
    retrieve cert using received APK info
    if !compare(Base64(SHA-1(cert)), hash) 
      return error

참고: http://dev.re.kr/70

## App Integrity Check를 회피 하지 못하게 하는 방법

### App의 필수 기능을 native에서 구현
WShoes의 경우, 서버 access token(EMP, google, facebook) 발급 API를 native에 구현 
    
    if (appIntegrityCheck())
        access_token = getAccessToken()
    return access_token


### App의 필수 기능을 encrypted하여 검증 후 decrypt
WShoes의 경우, 인증 관련 class(EMP, google, facebook 등)을 별도 dex로 분리 

    multi dex 구성으로 변경 필요, 2개 이상의 dex를 생성하도록 앱 빌드 프로세스를 구성해야 함 

    생성한 dex를 encryption 
    dexClassLoader를 이용하여 (decrypted)dex를 동적으로 loading

    if (appIntegritiyCheck())
        decrypt(dex)
        dynamic class loading (dex)


multi dex 사용하는 방법  
http://sjava.net/2016/12/%EB%A9%80%ED%8B%B0-dex-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0/

dexClassLoader를 이용한 동적 로딩
http://www.marioalmeida.eu/2015/01/27/how-to-easy-way-load-apk-classes-using-dexclassloader/#comment-1095
http://javacan.tistory.com/entry/1
http://jjun5050.tistory.com/27
