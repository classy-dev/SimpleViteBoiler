# SimpleViteBoiler

<img src="https://vitejs.dev/logo-with-shadow.png" alt="Vite" width="150" height="150" />

## 프로젝트 개요

이 프로젝트는 모던 웹 개발을 위한 최적화된 Vite 보일러플레이트입니다. 프로젝트 시작 시 반복되는 설정 작업을 줄이고, 즉시 개발에 집중할 수 있는 환경을 제공합니다. Webpack 대비 빠른 개발 서버와 빌드 시간을 제공하면서도 프로덕션 최적화 기능을 포함합니다.

### 주요 기술 스택

- **React 18**: 최신 리액트 기능 활용
- **TypeScript**: 타입 안정성 강화
- **Vite 6**: 차세대 프론트엔드 빌드 도구
- **Emotion**: CSS-in-JS 스타일링
- **React Router**: 클라이언트 라우팅
- **ESLint & Prettier**: 코드 품질 관리

## 구현 특징

### 1. 고급 Vite 구성

```typescript
// vite.config.ts의 주요 설정
export default defineConfig(({ command, mode }: ConfigEnv): UserConfig => {
  // 환경 변수 로드
  const env = loadEnv(mode, process.cwd(), "");

  // 개발/프로덕션 모드에 따른 설정 분기
  const isProduction = mode === "production";

  return {
    // 기본 설정
    base: "./",
    plugins: [
      // React 및 다양한 플러그인
    ],
    build: {
      // 빌드 최적화
    },
  };
});
```

이 설정은 개발 및 프로덕션 환경 모두에서 최적의 성능을 제공합니다.

### 2. 파일 기반 라우팅

Vite의 Pages 플러그인을 사용하여 Next.js와 유사한 파일 기반 라우팅을 구현했습니다:

```typescript
Pages({
  dirs: "src/pages",
  importMode: (path) => {
    if (path.includes("admin")) {
      return "sync"; // admin 페이지는 동적 로드하지 않고 정적 로드
    }
    return "async"; // 나머지는 동적 로드
  },
  extensions: ["tsx"],
  routeStyle: "next",
});
```

이 설정으로 `src/pages` 디렉토리에 파일을 추가하는 것만으로도 자동으로 라우트가 생성됩니다.

### 3. 최적화된 번들링

다양한 최적화 전략을 적용하여 빠른 로딩 시간과 효율적인 캐싱을 가능하게 합니다:

```typescript
build: {
  rollupOptions: {
    output: {
      manualChunks(id) {
        if (id.includes('node_modules')) {
          // React와 관련 라이브러리들
          if (id.includes('react') ||
              id.includes('react-dom') ||
              id.includes('scheduler')) {
            return 'react-vendor'
          }

          // 자주 변경되지 않는 큰 라이브러리들
          if (id.includes('lodash') ||
              id.includes('axios') ||
              id.includes('dayjs') ||
              id.includes('zustand') ||
              id.includes('react-query') ||
              id.includes('react-router-dom')) {
            return 'core-vendor'
          }
        }
      }
    }
  }
}
```

### 4. 퍼포먼스 최적화 기능

프로덕션 성능을 최대화하기 위한 다양한 기능을 제공합니다:

```typescript
// GZIP 압축
viteCompression({
  verbose: true,
  disable: !isProduction,
  threshold: 10240,
  algorithm: "gzip",
  ext: ".gz",
}),
  // Brotli 압축 추가
  viteCompression({
    verbose: true,
    disable: !isProduction,
    threshold: 10240,
    algorithm: "brotliCompress",
    ext: ".br",
  });
```

### 5. PWA 지원

Progressive Web App(PWA) 기능으로 오프라인 작동 및 앱 경험을 지원합니다:

```typescript
VitePWA({
  registerType: "autoUpdate",
  includeAssets: ["favicon.ico", "apple-touch-icon.png", "masked-icon.svg"],
  manifest: {
    name: "내 Vite 앱",
    short_name: "Vite 앱",
    theme_color: "#ffffff",
    // ...
  },
  workbox: {
    globPatterns: ["**/*.{js,css,html,ico,png,svg,woff2}"],
    runtimeCaching: [
      // API 캐싱 설정
    ],
  },
});
```

## 프로젝트 구조

```
SimpleViteBoiler/
├── public/                # 정적 파일
├── src/                   # 소스 코드
│   ├── assets/            # 이미지, 폰트 등
│   ├── components/        # 재사용 가능한 컴포넌트
│   ├── pages/             # 페이지 컴포넌트 (자동 라우팅)
│   ├── types/             # TypeScript 타입 정의
│   ├── App.jsx            # 메인 앱 컴포넌트
│   └── main.tsx           # 앱 진입점
├── vite.config.ts         # Vite 설정
├── tsconfig.json          # TypeScript 설정
├── eslint.config.js       # ESLint 설정
├── package.json           # 의존성 및 스크립트
└── README.md              # 프로젝트 문서
```

## 설치 및 실행

```bash
# 의존성 설치
npm install
# 또는
yarn install

# 개발 서버 실행 (핫 리로딩)
npm run dev
# 또는
yarn dev

# 프로덕션 빌드
npm run build
# 또는
yarn build

# 빌드 결과물 미리보기
npm run preview
# 또는
yarn preview
```

## 주요 최적화 기법

### 1. 스마트한 코드 분할

라이브러리 특성과 사용 패턴에 따라 최적화된 청크 분할 전략을 구현했습니다:

- React 관련 라이브러리는 별도 청크로 분리
- 큰 핵심 라이브러리(axios, router 등)는 별도 청크로 분리
- UI 라이브러리는 별도 청크로 관리

### 2. 이중 압축 전략

GZIP과 Brotli 압축을 모두 지원하여 다양한 클라이언트 환경에서 최적의 로딩 성능을 제공합니다.

### 3. 자산 관리 최적화

이미지와 폰트 등 정적 자산에 대한 스마트한 처리 로직을 구현했습니다:

```typescript
assetFileNames: (assetInfo) => {
  // 파일 타입에 따라 적절한 디렉토리에 배치
  if (extType && /png|jpe?g|svg|gif|tiff|bmp|ico/i.test(extType)) {
    return `images/${dirPath}[name].[hash][extname]`;
  }
  if (extType && /woff|woff2|eot|ttf|otf/i.test(extType)) {
    return `fonts/${dirPath}[name].[hash][extname]`;
  }
  return "[name].[hash][extname]";
};
```

## Vite 구성 주요 요소

1. **개발 서버**: HMR(Hot Module Replacement)과 빠른 개발 환경 제공
2. **플러그인 시스템**: 확장 가능한 빌드 파이프라인
3. **ES 모듈 기반**: 네이티브 ESM을 활용한 고속 번들링
4. **최적화된 빌드**: 프로덕션 빌드 시 코드 분할 및 최적화
5. **TypeScript 지원**: 기본적인 타입스크립트 통합
6. **환경 변수 처리**: 다양한 환경에 대한 설정 관리
7. **정적 자산 처리**: 이미지, 폰트 등의 효율적 처리

## 요약 & 활용방법

이 보일러플레이트는 Vite의 뛰어난 개발 경험과 프로덕션 성능을 모두 활용할 수 있도록 설계되었습니다. 개발자는 복잡한 빌드 구성에 시간을 낭비하지 않고 바로 핵심 비즈니스 로직 개발에 집중할 수 있습니다. 필요에 따라 설정을 쉽게 확장하거나 수정할 수 있도록 모듈화된 구조로 구성되어 있어, 다양한 프로젝트 요구사항에 유연하게 대응할 수 있습니다.
