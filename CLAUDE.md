# task-board

タスク管理ボードアプリケーションのプロジェクト。

## Git 運用ルール

**コードを変更するたびに、必ず GitHub にプッシュすること。**

具体的な手順:

1. 変更内容を確認する (`git status`, `git diff`)
2. 関連ファイルをステージングする (`git add <files>`)
3. 変更内容を表す明確なメッセージでコミットする (`git commit -m "..."`)
4. リモートリポジトリへプッシュする (`git push`)

補足事項:

- 小さな単位でコミットし、こまめにプッシュする (作業の損失を防ぐため)
- コミットメッセージは「なぜ変更したか」が分かるように書く
- `main` ブランチへの force push は禁止
- 機密情報 (`.env`, 認証情報など) はコミットしない
- pre-commit フック等のチェックをスキップしない (`--no-verify` 不可)

## デプロイ先

https://cheryy1974.github.io/task-board/

- `main` ブランチへの push を契機に GitHub Actions (`.github/workflows/deploy.yml`) が自動でビルド・公開する。
- Vite の `base` は `/task-board/` に固定 (`vite.config.ts`)。ローカル開発時のURLも `http://localhost:5173/task-board/` となる。

## 技術スタック

- **言語**: TypeScript (strict, `noUnusedLocals` / `noUnusedParameters` 有効)
- **UI フレームワーク**: React 18 (関数コンポーネント + Hooks のみ)
- **ビルドツール**: Vite 5 (`@vitejs/plugin-react`)
- **スタイリング**: 素の CSS (CSS-in-JS や Tailwind 等のフレームワークは使わない)
- **永続化**: ブラウザの `localStorage` (キー: `task-board:tasks`)
- **ホスティング**: GitHub Pages (静的ホスティング、サーバーサイド処理なし)
- **CI/CD**: GitHub Actions (Node 20, `npm ci` → `npm run build` → `actions/deploy-pages`)

ライブラリ追加時は上記方針との整合性を優先する (例: 状態管理ライブラリは Hooks で足りる限り導入しない)。

## コンポーネント・命名規約

### ファイル / 配置

- React コンポーネントは `src/` 配下に `PascalCase.tsx` で配置 (例: `App.tsx`)
- コンポーネント専用のスタイルは同名 `.css` を同階層に置く (例: `App.tsx` ↔ `App.css`)
- グローバルスタイルは `src/index.css`、エントリポイントは `src/main.tsx`

### コンポーネント

- 関数コンポーネント + Hooks のみ。クラスコンポーネントは使わない
- 1 ファイル = 1 コンポーネント (`export default`)
- 補助的な型・定数・純粋関数は同ファイル内に同居させてよい (`type Task`, `STORAGE_KEY`, `loadTasks` 等)

### 識別子

- **コンポーネント / 型 (`type`, `interface`)**: `PascalCase` (例: `App`, `Task`)
- **変数 / state / ハンドラ / 関数**: `camelCase` (例: `tasks`, `input`, `addTask`, `toggleTask`, `deleteTask`)
- **モジュールスコープの定数**: `SCREAMING_SNAKE_CASE` (例: `STORAGE_KEY`)
- **イベントハンドラ**: `動詞 + 対象` の形 (`addTask`, `toggleTask`)。`handleXxx` プレフィックスは使わない

### CSS クラス

- `kebab-case` + BEM 風の修飾子 (`--`) を採用
  - ブロック: `.task-item`
  - 修飾子: `.task-item--done`
  - 要素 (必要な場合): `.task-item__label` のように `__` を使う
- クラス名は意味 (役割) で付け、見た目 (`.red`, `.large` 等) では付けない

## 開発の進め方

- 変更は最小限のスコープに留める。タスクで求められていないリファクタや抽象化は行わない。
- 不要なコメントは書かない。コード自体で意図が伝わるように命名する。
- UI 変更時は実際にブラウザで動作確認してから完了報告する。
