[index.html](https://github.com/user-attachments/files/29496119/index.html)
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>マウスピース装着記録</title>
    <style>
        body { font-family: sans-serif; text-align: center; padding: 20px; }
        button { width: 90%; height: 120px; font-size: 24px; margin: 20px 0; border-radius: 15px; border: none; color: white; cursor: pointer; }
        .attach { background-color: #28a745; }
        .detach { background-color: #dc3545; }
    </style>
</head>
<body>
    <h2 id="patientName">読み込み中...</h2>
    <button class="attach" onclick="saveRecord('attach')">【装着しました】</button>
    <button class="detach" onclick="saveRecord('detach')">【外しました】</button>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/9.22.0/firebase-app.js";
        import { getFirestore, collection, addDoc, serverTimestamp, doc, getDoc } from "https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore.js";
        import { getAnalytics } from "https://www.gstatic.com/firebasejs/9.22.0/firebase-analytics.js";

        // あなたの設定を組み込みました
        const firebaseConfig = {
            apiKey: "AIzaSyA6_Wnmfgy28yfNo8E1ytbBwwTRrfL1zCs",
            authDomain: "mouthpiece-greenpark.firebaseapp.com",
            projectId: "mouthpiece-greenpark",
            storageBucket: "mouthpiece-greenpark.firebasestorage.app",
            messagingSenderId: "795688174725",
            appId: "1:795688174725:web:a0958e30465e09e51495ec",
            measurementId: "G-V8CC0XRT2T"
        };

        const app = initializeApp(firebaseConfig);
        const db = getFirestore(app);
        const analytics = getAnalytics(app);

        // URLからIDを取得 (例: index.html?id=あなたの患者ドキュメントID)
        const urlParams = new URLSearchParams(window.location.search);
        const pId = urlParams.get('id');

        // 患者名を表示
        async function loadPatient() {
            if (!pId) {
                document.getElementById('patientName').innerText = "IDが指定されていません";
                return;
            }
            try {
                const docRef = doc(db, "patients", pId);
                const docSnap = await getDoc(docRef);
                if (docSnap.exists()) {
                    document.getElementById('patientName').innerText = docSnap.data().name + " 様";
                } else {
                    document.getElementById('patientName').innerText = "患者が見つかりません";
                }
            } catch (e) {
                console.error(e);
            }
        }
        loadPatient();

        // 記録保存
        window.saveRecord = async (type) => {
            if (!pId) { alert("患者IDが指定されていません"); return; }
            try {
                await addDoc(collection(db, "records"), {
                    patientId: pId,
                    action: type,
                    timestamp: serverTimestamp()
                });
                alert("記録しました！");
            } catch (e) {
                alert("エラーが発生しました: " + e.message);
            }
        };
    </script>
</body>
</html>
