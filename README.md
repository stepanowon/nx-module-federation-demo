# nx-module-federation-demo
nx를 이용한 모듈 피더레이션 데모 스크립트
---
## 사전 준비 사항
* Node.js 24( 22 버전 이상 )
* 인터넷 연결
* nx 21버전
  
## 완성 후 구조
```
nx-module-federation-demo/
├── apps/
│   ├── host-app/     # 호스트 앱 (포트 4200)
│   ├── app1/         # 리모트 앱 1 (포트 4201)
│   └── app2/         # 리모트 앱 2 (포트 4202)
└── 실행 스크립트들
```
---
# 실행 단계
### NX 워크스페이스 초기화
```
# 프로젝트 디렉토리 생성
mkdir nx-module-federation-demo
cd nx-module-federation-demo
# Nx 워크스페이스 생성
npx create-nx-workspace@21 . --preset=apps --name=nx-federation-demo --packageManager=npm --ci=skip
# NX용 React 플러그인 추가
npx nx add @nx/react
```

### 호스트앱, 리모트앱 생성
* 명령어 하나로 모든앱을 생성
```
# Host 앱과 2개의 리모트 앱을 동시에 생성
# 모듈 피더레이션 관련 자동 설정
npx nx g @nx/react:host apps/host-app --remotes=app1,app2 --style=css --e2eTestRunner=none
```
### 여기까지 수행후 VSCode로 오픈
---

# 각 앱별 코드 수정 
* BootStrap 패턴으로 앱 구성
## app1 코드 변경
* apps/app1/src/bootstrap.tsx 변경
```
import { StrictMode } from 'react';
import * as ReactDOM from 'react-dom/client';
import { BrowserRouter } from 'react-router-dom';
import App from './app/app';

// 독립 실행용 Bootstrap
const root = ReactDOM.createRoot(
  document.getElementById('root') as HTMLElement
);

root.render(
  <StrictMode>
    <BrowserRouter>
      <App />
    </BrowserRouter>
  </StrictMode>
);
```
* apps/app1/src/app/app.tsx 변경
```
import { Routes, Route, Link, useLocation } from 'react-router-dom';

function Dashboard() {
  return (
    <div style={{ padding: '10px', backgroundColor: '#f0f8ff' }}>
      <h3>App1 대시보드</h3>
      <p>App1의 대시보드 화면</p>
    </div>
  );
}

function Profile() {
  return (
    <div style={{ padding: '10px', backgroundColor: '#f0f8ff' }}>
      <h3>App1 프로필</h3>
      <p>App1의 프로필 화면</p>
    </div>
  );
}

function Settings() {
  return (
    <div style={{ padding: '10px', backgroundColor: '#f0f8ff' }}>
      <h3>App1 설정</h3>
      <p>App1의 설정 화면</p>
    </div>
  );
}

export function App() {
  const location = useLocation();
  const isInHost = location.pathname.startsWith('/app1');
  
  return (
    <div style={{ padding: '20px', border: '2px solid blue' }}>
      <h1>App1 - Remote 컴포넌트</h1>
      <p>현재 요청 경로 : {location.pathname}</p>
      <p>피더레이션 상태 : {isInHost ? '호스트앱에서 피더레이션! ' : '독립 실행'}</p>
      
      <nav style={{ margin: '10px 0', padding: '10px', backgroundColor: '#e6f3ff' }}>
        {isInHost ? (
          <>
            <Link to="/app1/dashboard" style={{ marginRight: '10px' }}>대시보드</Link>
            <Link to="/app1/profile" style={{ marginRight: '10px' }}>프로필</Link>
            <Link to="/app1/settings" style={{ marginRight: '10px' }}>설정</Link>
          </>
        ) : (
          <>
            <Link to="/dashboard" style={{ marginRight: '10px' }}>대시보드</Link>
            <Link to="/profile" style={{ marginRight: '10px' }}>프로필</Link>
            <Link to="/settings" style={{ marginRight: '10px' }}>설정</Link>
          </>
        )}
      </nav>

      <Routes>
        {/* 독립 실행용 라우트 */}
        <Route path="/dashboard" element={<Dashboard />} />
        <Route path="/profile" element={<Profile />} />
        <Route path="/settings" element={<Settings />} />
        
        {/* Host에서 피더레이션될때 라우트 */}
        <Route path="/app1/dashboard" element={<Dashboard />} />
        <Route path="/app1/profile" element={<Profile />} />
        <Route path="/app1/settings" element={<Settings />} />
        
        {/* 홈 라우트 */}
        <Route path="/" element={
          <div style={{ padding: '10px' }}>
            <h3>App1 홈</h3>
            <p>App1의 홈 화면</p>
            <p>독립 실행중</p>
          </div>
        } />
        <Route path="/app1" element={
          <div style={{ padding: '10px' }}>
            <h3>App1 홈</h3>
            <p>App1의 홈 화면</p>
            <p>호스트 앱에서 피더레이션 중</p>
          </div>
        } />
      </Routes>
    </div>
  );
}

export default App;
```  
---
## app2 코드 변경
* apps/app2/src/bootstrap.tsx 변경
```
import { StrictMode } from 'react';
import * as ReactDOM from 'react-dom/client';
import { BrowserRouter } from 'react-router-dom';
import App from './app/app';

// 독립 실행용 Bootstrap
const root = ReactDOM.createRoot(
  document.getElementById('root') as HTMLElement
);

root.render(
  <StrictMode>
    <BrowserRouter>
      <App />
    </BrowserRouter>
  </StrictMode>
);
```
* apps/app2/src/app/app.tsx 변경
```
import { Routes, Route, Link, useLocation } from 'react-router-dom';

function Stats() {
  return (
    <div style={{ padding: '10px', backgroundColor: '#f0fff0' }}>
      <h3>App2 스탯</h3>
      <p>App2의 스탯 화면/p>
      <ul>
        <li>사용자: 1,234</li>
        <li>성장율: +15%</li>
      </ul>
    </div>
  );
}

function Reports() {
  return (
    <div style={{ padding: '10px', backgroundColor: '#f0fff0' }}>
      <h3>App2의 리포트</h3>
      <p>App2의 분기별 리포트 화면</p>
      <ul>
        <li>1분기 리포트: 완료</li>
        <li>2분기 리포트: 처리중</li>
      </ul>
    </div>
  );
}

function Analytics() {
  return (
    <div style={{ padding: '10px', backgroundColor: '#f0fff0' }}>
      <h3>App2의 분석</h3>
      <p>App2의 상세 분석 화면</p>
      <ul>
        <li>페이지 뷰 : 45,678</li>
        <li>전환율 : 23%</li>
      </ul>
    </div>
  );
}

export function App() {
  const location = useLocation();
  const isInHost = location.pathname.startsWith('/app2');
  
  return (
    <div style={{ padding: '20px', border: '2px solid green' }}>
      <h1>App2 - Remote 컴포넌트</h1>
      <p>현재 요청 경로 : {location.pathname}</p>
      <p>피더레이션 상태 : {isInHost ? '호스트앱에서 피더레이션! ' : '독립 실행'}</p>
      
      <nav style={{ margin: '10px 0', padding: '10px', backgroundColor: '#e6ffe6' }}>
        {isInHost ? (
          <>
            <Link to="/app2/stats" style={{ marginRight: '10px' }}>스탯</Link>
            <Link to="/app2/reports" style={{ marginRight: '10px' }}>리포트</Link>
            <Link to="/app2/analytics" style={{ marginRight: '10px' }}>분석</Link>
          </>
        ) : (
          <>
            <Link to="/stats" style={{ marginRight: '10px' }}>스탯</Link>
            <Link to="/reports" style={{ marginRight: '10px' }}>리포트</Link>
            <Link to="/analytics" style={{ marginRight: '10px' }}>분석</Link>
          </>
        )}
      </nav>

      <Routes>
        {/* 독립 실행용 라우트 */}
        <Route path="/stats" element={<Stats />} />
        <Route path="/reports" element={<Reports />} />
        <Route path="/analytics" element={<Analytics />} />
        
        {/* Host에서 피더레이션될때 라우트 */}
        <Route path="/app2/stats" element={<Stats />} />
        <Route path="/app2/reports" element={<Reports />} />
        <Route path="/app2/analytics" element={<Analytics />} />
        
        {/* 홈 라우트 */}
        <Route path="/" element={
          <div style={{ padding: '10px' }}>
            <h3>App2 홈</h3>
            <p>App2의 홈 화면</p>
            <p>독립 실행중</p>
          </div>
        } />
        <Route path="/app2" element={
          <div style={{ padding: '10px' }}>
            <h3>App2 홈</h3>
            <p>App2의 홈 화면</p>
            <p>호스트 앱에서 피더레이션 중</p>
          </div>
        } />
      </Routes>
    </div>
  );
}

export default App;
```
---
## host-app 코드 변경
* apps/host-app/src/app/app.tsx 변경
```
import * as React from 'react';
import { Link, Route, Routes, useLocation } from 'react-router-dom';

const App1 = React.lazy(() => import('app1/Module'));
const App2 = React.lazy(() => import('app2/Module'));

// Host에서 App1/App2를 래핑
function App1Wrapper() {
  return (
    <div style={{ padding: '10px', backgroundColor: '#f8f9fa' }}>
      <h2>app1 영역</h2>
      <React.Suspense fallback={<div>app1 로딩중...</div>}>
        <App1 />
      </React.Suspense>
    </div>
  );
}

function App2Wrapper() {
  return (
    <div style={{ padding: '10px', backgroundColor: '#f8f9fa' }}>
      <h2>app2 영역</h2>
      <React.Suspense fallback={<div>app2 로딩중...</div>}>
        <App2 />
      </React.Suspense>
    </div>
  );
}

export function App() {
  const location = useLocation();
  
  return (
    <div style={{ padding: '20px', fontFamily: 'Arial, sans-serif' }}>
      <h1>호스트앱 - Bootstrap 패턴</h1>
      <p>현재 요청 경로 : {location.pathname}</p>
      
      <nav style={{ margin: '20px 0', padding: '15px', backgroundColor: '#e9ecef', borderRadius: '5px' }}>
        <Link to="/" style={{ marginRight: '15px', textDecoration: 'none' }}>🏠 Home</Link>
        <Link to="/app1" style={{ marginRight: '15px', textDecoration: 'none' }}>📘 App1</Link>
        <Link to="/app2" style={{ marginRight: '15px', textDecoration: 'none' }}>📗 App2</Link>
      </nav>

      <div style={{ border: '1px solid #ddd', borderRadius: '5px', padding: '20px' }}>
        <Routes>
          <Route 
            path="/" 
            element={
              <div>
                <h2>호스트 앱 데모</h2>
                <p>app1, app2는 bootstrap.tsx를 통해 독립 실행 가능</p>
              </div>
            } 
          />
          <Route path="/app1/*" element={<App1Wrapper />} />
          <Route path="/app2/*" element={<App2Wrapper />} />
        </Routes>
      </div>
    </div>
  );
}

export default App;
```
---
# 테스트
* host-app 실행
```
# 다음 명령어를 실행하면 리모트 앱(app1, app2)도 함께 실행됨
npx nx serve host-app
# http://localhost:4200으로 테스트
```

* app1, app2 독립 실행
```
# 리모트 앱 중 하나를 실행하면 호스트 앱이 함께 실행됨
# http://localhost:4201 로 독립실행 테스트
npx nx serve app1

# http://localhost:4202 로 독립실행 테스트
npx nx serve app2
```
### 윈도우 파워셸에서 혹시 실행되지 않으면 다음 명령어 실행
```
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```








