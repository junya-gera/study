https://www.youtube.com/watch?v=JE5xfZo7_Qc

"use client" をつけるとクライアントコンポーネント、つけないとサーバーコンポーネントになる。
useState や useEffect などの React Hooks はクライアントで実行されるので
"use client" がないと使えない。

リアルタイム性のある、インタラクティブなアプリを作りたい場合は
ブラウザ側、クライアント側で値を操作、管理したほうがユーザビリティが上がる。
→ クライアントサイドレンダリング (CSR)