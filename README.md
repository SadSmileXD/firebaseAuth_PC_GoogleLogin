**※해당 문서는 아무것도 모르는 분들을 위해 최대한 자세하게 작성 했습니다.**


# 수정 사항
- 에디터 타임에서만 테스트를 진행했는데 통신 지연 발생으로 코드 수정함.  
수정 이후 지연 시간 단축 기존 3분 현 10초 내  


- 목차
    - [1. 구글 클라우드 설정 방법](#구글-클라우드-설정하기)
    - [2. firebase 설정 하기](#firebase-설정하기)


----
### 구글 클라우드 설정하기.
 [`구글 클라우드 바로가기`](https://cloud.google.com/?hl=ko)

해당 구글 클라우드 접속을 한다.

![alt text](https://github.com/SadSmileXD/firebaseAuth_PC_GoogleLogin/blob/main/image/image.png)
- Agent Platform 클릭

 

![alt text](https://github.com/SadSmileXD/firebaseAuth_PC_GoogleLogin/blob/main/image/image-1.png)

- Google Cloud 옆에 클릭

![alt text](https://github.com/SadSmileXD/firebaseAuth_PC_GoogleLogin/blob/main/image/image-2.png)

- 새 프로젝트 만들기 클릭

![alt text](https://github.com/SadSmileXD/firebaseAuth_PC_GoogleLogin/blob/main/image/image-3.png)

- 설정 후 만들기 클릭

![alt text](https://github.com/SadSmileXD/firebaseAuth_PC_GoogleLogin/blob/main/image/image-4.png)
- API 및 서비스 클릭

![alt text](https://github.com/SadSmileXD/firebaseAuth_PC_GoogleLogin/blob/main/image/image-5.png)

- 사용자 인증 정보 클릭

![alt text](https://github.com/SadSmileXD/firebaseAuth_PC_GoogleLogin/blob/main/image/image-6.png)

- 사용자 인증 정보 만들기 클릭

![alt text](https://github.com/SadSmileXD/firebaseAuth_PC_GoogleLogin/blob/main/image/image-7.png)

- OAuth 클라이언트 ID 클릭

![alt text](https://github.com/SadSmileXD/firebaseAuth_PC_GoogleLogin/blob/main/image/image-8.png)

- 동의 화면 구성 클릭

![alt text](https://github.com/SadSmileXD/firebaseAuth_PC_GoogleLogin/blob/main/image/image-9.png)

- 시작하기 클릭

![alt text](https://github.com/SadSmileXD/firebaseAuth_PC_GoogleLogin/blob/main/image/image-10.png)

- 『 앱 이름 』설정 후 사용자 『 이메일 지정 』 다음 클릭

![alt text](https://github.com/SadSmileXD/firebaseAuth_PC_GoogleLogin/blob/main/image/image-11.png)

- 나는 외부 설정함.

![alt text](https://github.com/SadSmileXD/firebaseAuth_PC_GoogleLogin/blob/main/image/image-12.png)

- 이메일 등록 후 다음 클릭

![alt text](https://github.com/SadSmileXD/firebaseAuth_PC_GoogleLogin/blob/main/image/image-13.png)

- 완료  체크 후 계속 클릭

![alt text](https://github.com/SadSmileXD/firebaseAuth_PC_GoogleLogin/blob/main/image/image-14.png)

- 완료 후 만들기 클릭

![alt text](https://github.com/SadSmileXD/firebaseAuth_PC_GoogleLogin/blob/main/image/image-15.png)

- 다시 사용자 인증 정보로 돌아오기.

![alt text](https://github.com/SadSmileXD/firebaseAuth_PC_GoogleLogin/blob/main/image/image-16.png)

- OAuth 클라이언트 ID 클릭

![alt text](https://github.com/SadSmileXD/firebaseAuth_PC_GoogleLogin/blob/main/image/image-17.png)
- 승인된 리디렉션 url 추가해야함.
- 『 http://127.0.0.1:7123/ 』 추가

![alt text](https://github.com/SadSmileXD/firebaseAuth_PC_GoogleLogin/blob/main/image/image-18.png)

![alt text](https://github.com/SadSmileXD/firebaseAuth_PC_GoogleLogin/blob/main/image/image-19.png)

기억 해둬여 할 클라이언트 ID / 클라이언트 보안 비밀번호 기억 또는 저장 해둬야함.

### ※여기까지가  구글 클라우드 설정※

---
# firebase 설정하기.
![alt text](https://github.com/SadSmileXD/firebaseAuth_PC_GoogleLogin/blob/main/image/image-20.png)

- firebase로 가서 Auth에 로그인 방법에 구글 사용처리 후
- 외부 프로젝트의 클라이언트 ID허용 목록에 추가 클릭  
(Auth에 구글 로그인 사용처리 해야 설정가능. ) 
 
![alt text](https://github.com/SadSmileXD/firebaseAuth_PC_GoogleLogin/blob/main/image/image-22.png)

- 아까 저장해둔  구글 클라우드 ID 삽입
- 구글 회원가입 / 자동로그인  코드
    
 ```csharp
  using System;
using System.IO;
using System.Net;
using System.Net.Sockets;
using System.Text;
using System.Threading.Tasks;
using UnityEngine;
using Firebase.Auth;
using UnityEngine.UI;
using UnityEngine.SceneManagement;

public class GoogleTokenFetcher : MonoBehaviour
{
    [Header("Firebase Web Client Credentials")]
    [Tooltip("GCP 콘솔의 웹 애플리케이션 클라이언트 ID")]
    [SerializeField] private string webClientId = "여기에_웹_클라이언트_ID_입력";

    [Tooltip("GCP 콘솔의 웹 애플리케이션 클라이언트 보안 비밀번호")]
    [SerializeField] private string webClientSecret = "여기에_웹_클라이언트_보안_비밀번호_입력";

    private const int LocalPort = 7123;

    private FirebaseAuth auth;

    public Button m_btn;

    private void Start()
    {
        auth = FirebaseAuth.DefaultInstance;
        if (m_btn != null)
        {
            m_btn.onClick.AddListener(GetGoogleToken);
        }
    }

    [ContextMenu("Get Google Token & Firebase Auth Test")]
    public async void GetGoogleToken()
    {
        Debug.Log("1. 구글 로그인 웹 브라우저를 엽니다...");

        TokenResponse tokenData = await FetchTokenFromGoogleAsync();

        if (tokenData != null && !string.IsNullOrEmpty(tokenData.id_token))
        {
            Debug.Log("<color=green><b>[구글 토큰 발급 성공!]</b></color>");
            Debug.Log($"<b>ID Token:</b>\n{tokenData.id_token}");

            await SignInWithFirebaseAsync(tokenData.id_token, tokenData.access_token);
        }
        else
        {
            Debug.LogError("구글 토큰 발급 실패");
        }
    }

    private async Task<TokenResponse> FetchTokenFromGoogleAsync()
    {
        string redirectUri = $"http://127.0.0.1:{LocalPort}/";
        TcpListener tcpListener = null;

        try
        {
            // IPAddress.Loopback (127.0.0.1) 바인딩으로 소켓 수신 시작
            tcpListener = new TcpListener(IPAddress.Loopback, LocalPort);
            tcpListener.Start();
        }
        catch (Exception ex)
        {
            Debug.LogError($"TcpListener 시작 실패 (포트 {LocalPort}가 이미 사용 중입니다): {ex.Message}");
            return null;
        }

        string authorizationEndpoint = "https://accounts.google.com/o/oauth2/v2/auth";
        string scope = Uri.EscapeDataString("openid email profile");

        StringBuilder urlBuilder = new StringBuilder();
        urlBuilder.Append($"{authorizationEndpoint}?");
        urlBuilder.Append($"response_type=code");
        urlBuilder.Append($"&client_id={webClientId}");
        urlBuilder.Append($"&redirect_uri={Uri.EscapeDataString(redirectUri)}");
        urlBuilder.Append($"&scope={scope}");

        Application.OpenURL(urlBuilder.ToString());

        string code = null;

        try
        {
            // 브라우저 리다이렉트 소켓 연결 대기
            using (TcpClient client = await tcpListener.AcceptTcpClientAsync())
            using (NetworkStream stream = client.GetStream())
            {
                // 1. Raw Byte 수신 (StreamReader의 개행 대기 타임아웃 차단)
                byte[] requestBuffer = new byte[2048];
                int bytesRead = await stream.ReadAsync(requestBuffer, 0, requestBuffer.Length);
                string requestText = Encoding.UTF8.GetString(requestBuffer, 0, bytesRead);

                if (!string.IsNullOrEmpty(requestText) && requestText.StartsWith("GET"))
                {
                    // "GET /?code=XXXXXX HTTP/1.1" 형태에서 code 추출
                    int codeIndex = requestText.IndexOf("code=");
                    if (codeIndex != -1)
                    {
                        string paramSubstring = requestText.Substring(codeIndex + 5);
                        int spaceOrAmp = paramSubstring.IndexOfAny(new char[] { ' ', '&' });
                        code = (spaceOrAmp != -1) ? paramSubstring.Substring(0, spaceOrAmp) : paramSubstring;
                        code = Uri.UnescapeDataString(code);
                    }
                }

                // 2. 브라우저 응답 HTML 생성
                string responseHtml = "<html><head><meta charset='utf-8'></head>" +
                                       "<body style='text-align:center; padding-top:50px; font-family:sans-serif;'>" +
                                       "<h1>Google Login Completed!</h1>" +
                                       "<p>You can close this window and return to Unity.</p>" +
                                       "</body></html>";

                byte[] htmlBytes = Encoding.UTF8.GetBytes(responseHtml);

                // 3. Raw HTTP 응답 헤더 작성 (Connection: close 필수)
                string header = "HTTP/1.1 200 OK\r\n" +
                                "Content-Type: text/html; charset=utf-8\r\n" +
                                $"Content-Length: {htmlBytes.Length}\r\n" +
                                "Connection: close\r\n\r\n";

                byte[] headerBytes = Encoding.UTF8.GetBytes(header);

                // 4. 헤더와 바디 전송 후 즉시 소켓 강제 종료 (Keep-Alive 대기 즉시 해제)
                await stream.WriteAsync(headerBytes, 0, headerBytes.Length);
                await stream.WriteAsync(htmlBytes, 0, htmlBytes.Length);
                await stream.FlushAsync();

                client.Close();
            }
        }
        catch (Exception ex)
        {
            Debug.LogError($"소켓 수신 중 오류 발생: {ex.Message}");
            return null;
        }
        finally
        {
            tcpListener.Stop();
        }

        if (string.IsNullOrEmpty(code))
        {
            Debug.LogError("인증 코드(Auth Code)를 파싱하지 못했습니다.");
            return null;
        }

        Debug.Log($"2. 인증 코드(Auth Code) 획득 성공");

        return await ExchangeCodeForTokenAsync(code, redirectUri);
    }

    private async Task<TokenResponse> ExchangeCodeForTokenAsync(string code, string redirectUri)
    {
        Debug.Log("3. 인증 코드로 구글 서버에 토큰 교환 요청 중...");

        string tokenEndpoint = "https://oauth2.googleapis.com/token";

        string postData = $"code={Uri.EscapeDataString(code)}" +
                          $"&client_id={Uri.EscapeDataString(webClientId)}" +
                          $"&client_secret={Uri.EscapeDataString(webClientSecret)}" +
                          $"&redirect_uri={Uri.EscapeDataString(redirectUri)}" +
                          $"&grant_type=authorization_code";

        byte[] data = Encoding.UTF8.GetBytes(postData);

        HttpWebRequest request = (HttpWebRequest)WebRequest.Create(tokenEndpoint);
        request.Method = "POST";
        request.ContentType = "application/x-www-form-urlencoded";
        request.ContentLength = data.Length;

        try
        {
            using (Stream stream = await request.GetRequestStreamAsync())
            {
                await stream.WriteAsync(data, 0, data.Length);
            }

            using (WebResponse response = await request.GetResponseAsync())
            using (StreamReader reader = new StreamReader(response.GetResponseStream()))
            {
                string json = await reader.ReadToEndAsync();
                return JsonUtility.FromJson<TokenResponse>(json);
            }
        }
        catch (WebException webEx)
        {
            if (webEx.Response != null)
            {
                using (StreamReader reader = new StreamReader(webEx.Response.GetResponseStream()))
                {
                    string errorResponse = await reader.ReadToEndAsync();
                    Debug.LogError($"[Google Response Error] {errorResponse}");
                }
            }
            return null;
        }
    }

    private async Task SignInWithFirebaseAsync(string idToken, string accessToken)
    {
        Debug.Log("4. Firebase Authentication에 인증 요청 중...");

        try
        {
            Credential credential = GoogleAuthProvider.GetCredential(idToken, accessToken);

            FirebaseUser user = await auth.SignInWithCredentialAsync(credential);

            if (user != null)
            {
                Debug.Log("<color=cyan><b>[Firebase 인증 및 회원가입 성공!]</b></color>");
                Debug.Log($"<b>Firebase UID:</b> {user.UserId}");
                Debug.Log($"<b>이름:</b> {user.DisplayName}");
                Debug.Log($"<b>이메일:</b> {user.Email}");

                SceneManager.LoadScene(2);
            }
        }
        catch (Exception ex)
        {
            Debug.LogError($"[Firebase 로그인 실패] {ex.Message}");
        }
    }

    [Serializable]
    public class TokenResponse
    {
        public string id_token;
        public string access_token;
        public int expires_in;
        public string token_type;
    }
}
    ```
    

![alt text](https://github.com/SadSmileXD/firebaseAuth_PC_GoogleLogin/blob/main/image/image-23.png)

코드에 아까 저장한 클라이언트 ID 와 비밀번호 넣기
![alt text](https://github.com/SadSmileXD/firebaseAuth_PC_GoogleLogin/blob/main/image/image-24.png)

- 컨텍스메뉴로 실행



나는 로그인 성공 이력이 있어서 이런 화면이 뜨는데 
계정 로그인하면된다.

![alt text](https://github.com/SadSmileXD/firebaseAuth_PC_GoogleLogin/blob/main/image/image-25.png)

![alt text](https://github.com/SadSmileXD/firebaseAuth_PC_GoogleLogin/blob/main/image/image-26.png)

계정 생성 결과.
