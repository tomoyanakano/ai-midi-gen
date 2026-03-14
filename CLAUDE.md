# ai-midi-gen

テキスト入力からMIDIを生成するWebサービス。

## プロジェクト概要

- 自然言語プロンプト（「Cmajorの4小節コード進行」）からMIDIを生成
- ABC記法・MMLなどのテキスト記法からもMIDI変換可能
- ブラウザ内で楽譜表示・プレビュー再生・MIDIダウンロード

## アーキテクチャ

```
ai-midi-gen/
├── frontend/          # Next.js (TypeScript)
│   ├── src/
│   │   ├── app/       # App Router
│   │   ├── components/
│   │   ├── hooks/
│   │   ├── lib/       # abcjs, Tone.js ラッパー
│   │   └── types/
│   └── package.json
├── backend/           # FastAPI (Python)
│   ├── app/
│   │   ├── api/       # エンドポイント
│   │   ├── services/  # LLM連携, MIDI生成ロジック
│   │   └── main.py
│   └── pyproject.toml
└── CLAUDE.md
```

## 技術スタック

### Frontend
- **Next.js 15** (App Router, TypeScript)
- **Tailwind CSS** - スタイリング
- **abcjs** - ABC記法 → 楽譜レンダリング (SVG) + MIDI出力
- **@tonejs/midi** - MIDI読み書き
- **Tone.js** - Web Audioプレビュー再生
- **Biome** - Linter / Formatter
- **pnpm** - パッケージマネージャー

### Backend
- **FastAPI** - APIフレームワーク
- **Python 3.12+** / **uv** - ランタイム / パッケージマネージャー
- **Anthropic SDK** - Claude API連携
- **music21** - 音楽理論処理・MIDI変換
- **Ruff** - Linter / Formatter

## 開発コマンド

```bash
# Frontend
cd frontend && pnpm dev          # 開発サーバー (localhost:3000)
cd frontend && pnpm build        # ビルド
cd frontend && pnpm lint         # Biome lint

# Backend
cd backend && uv run fastapi dev # 開発サーバー (localhost:8000)
cd backend && uv run ruff check  # Ruff lint
```

## API設計

### `POST /api/generate`

自然言語プロンプトからABC記法を生成しMIDIを返す。

```json
// Request
{
  "prompt": "Cmajorの4小節コード進行、テンポ120、ピアノ",
  "mode": "prompt"
}

// Response
{
  "abc": "X:1\nT:Generated\nM:4/4\nK:C\n...",
  "midi_base64": "TVRoZC..."
}
```

### `POST /api/convert`

ABC記法/MMLからMIDIに変換する。

```json
// Request
{
  "notation": "X:1\nT:Example\nM:4/4\nK:C\n|: C E G c |",
  "format": "abc"
}

// Response
{
  "midi_base64": "TVRoZC..."
}
```

### `GET /api/health`

ヘルスチェック。

## コーディング規約

- **不変性**: オブジェクトのミューテーション禁止、常に新しいオブジェクトを返す
- **ファイルサイズ**: 1ファイル400行以下を目安、800行を超えない
- **関数サイズ**: 1関数50行以下
- **エラーハンドリング**: try-catchで適切にハンドル、ユーザーフレンドリーなメッセージ
- **型安全**: TypeScript strict mode、Pythonは型ヒント必須
- **コミット**: Conventional Commits (`feat:`, `fix:`, `refactor:` 等)

## フェーズ計画

- **Phase 1 (MVP)**: ABC記法入力→楽譜表示+MIDI出力 / 自然言語→MIDI生成
- **Phase 2**: MML対応 / 複数トラック / GM音源プレビュー
- **Phase 3**: Image to MIDI
- **Phase 4**: Ableton Live M4L連携

## 参考リソース

- [abcjs公式ドキュメント](https://www.abcjs.net/)
- [Tone.js](https://tonejs.github.io/)
- [music21](https://pypi.org/project/music21/)
- [Anthropic Python SDK](https://github.com/anthropics/anthropic-sdk-python)
