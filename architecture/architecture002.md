## 設計

### 設計 A

- Model、View、ViewModel、API、 は、そのままディレクトリ構成となる

- データバインディング

  - ViewModel - View 間、Model - ViewModel 間で、双方向バインディング

- API 通信
  - API モジュール には、すべての API の URL、エンドポイント、DataClass、リクエストヘッダー情報が記述されている

```mermaid
graph TB
	subgraph "View"

		View000("Video Feed (動画フィード)")
		View001("PlaybackScreen (再生スクリーン)")
		View002("Video Feed Detail (詳細)")
		View003("Video Comments (コメント領域)")
		View004("Video Posting (動画投稿)")
		View005("User Information (ユーザー情報画面)")
		View006("Settings (管理画面)")

	end

	subgraph "ViewModel"

		ViewModel000("Video Feed (動画フィード)")
		ViewModel001("Video Posting (動画投稿)")
		ViewModel002("User Information (ユーザー情報画面)")
		ViewModel003("Settings (管理画面)")

	end

	subgraph "Model"
		Model000("User Service")
		Model001("Video Feed Service")
		Model002("Comments Service")
		Model003("Video Playback")

		Model004("Video Feed (動画フィード)")
		Model005("Video Posting (動画投稿)")
		Model006("User Information (ユーザー情報画面)")
		Model007("Settings (管理画面)")

	end

	API000("API 通信")

	API000 --> Model000
	API000 --> Model001
	API000 --> Model002

	Model000 --> Model004
	Model000 --> Model005
	Model000 --> Model006
	Model000 --> Model007
	Model001 --> Model004
	Model002 --> Model004
	Model003 --> Model004

	Model004 --> ViewModel000
	Model005 --> ViewModel001
	Model006 --> ViewModel002
	Model007 --> ViewModel003

	ViewModel000 --> View000
	ViewModel001 --> View004
	ViewModel002 --> View005
	ViewModel003 --> View006

	ViewModel000 --> View001
	ViewModel000 --> View002
	ViewModel000 --> View003
```

### 設計 B

- 機能ごと(Core, Scene)に外部モジュール化されている
- 機能ごと(Core, Scene)の View には ViewModel が付随する
- Core は Service 単位で独立した外部モジュールとしている

- データバインディング

  - Core 層から 単方向バインディング

- API 通信
  - モジュール内の Service コンポーネントごとに API の URL、エンドポイント、DataClass、リクエストヘッダー情報が分散管理されている

#### APP

```mermaid
graph RL
	subgraph APP
		subgraph Scene["APP Scene (各機能モジュール)"]
			Scene000("Video Feed (動画フィード)")
			Scene001("Video Posting (動画投稿)")
			Scene002("User Information (ユーザー情報画面)")
			Scene003("Settings (管理画面)")
		end
		subgraph Core["APP Core (全体モジュール)"]
			Core000("Utils")
			Core001("User Service")
			Core002("Video Playback")
		end
	end
```

#### Scene : Video Feed (動画フィード)

```mermaid
graph TD
	subgraph Scene["Video Feed (動画フィード)"]
		subgraph "View"
			Component000("PlaybackScreen (再生スクリーン)")
			Component001("Detail (詳細領域)")
			Component002("Comments (コメント領域)")
		end

		subgraph "Core"
			Service000("Video Feed Service")
		end

		Service000 --> Component000
		Service000 --> Component001
		Service000 --> Component002

	end
```

##### Scene : Video Feed (動画フィード) / Detail (詳細)

```mermaid
graph TD
	subgraph Scene["Detail (詳細領域)"]
		subgraph "View"
			subgraph "Detail (詳細)"
				Component000("Icon (ユーザーアイコン)")
				Component001("Icon (ユーザー名)")
				Component002("Title (動画タイトル)")
			end
		end
		subgraph "Core"
			Service000("Video Feed Service")
		end
		subgraph "APP Core (全体モジュール)"
			Core000("Utils")
			Core001("User Service")
		end

		Core000 --> Component000
		Core000 --> Component001
		Core000 --> Component002
		Core001 --> Component000
		Core001 --> Component001
		Service000 --> Component002
	end
```

##### Scene : Video Feed (動画フィード) / Comments (コメント領域)

```mermaid
graph TD
	subgraph Scene["Comments (コメント領域)"]
		subgraph "View"
			subgraph "Comment (コメント)"
				Component000("Icon (ユーザーアイコン)")
				Component001("Icon (ユーザー名)")
				Component002("Text (コメント)")
			end
		end
		subgraph "Core"
			Service000("Comment Service")
		end
		subgraph "APP Core (全体モジュール)"
			Core000("Utils")
			Core001("User Service")
		end

		Core000 --> Component000
		Core000 --> Component001
		Core000 --> Component002
		Core001 --> Component000
		Core001 --> Component001
		Service000 --> Component002
	end
```
