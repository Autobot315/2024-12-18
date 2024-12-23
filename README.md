import React, { useState, useEffect } from "react";

// ストレス解消法データ
const stressReliefData = [
  { ageRange: "10-14", methods: ["短時間の瞑想", "好きな音楽を聴く", "ペットと遊ぶ", "自然の中で散歩"] },
  { ageRange: "15-19", methods: ["スポーツで体を動かす", "友達と話す", "趣味に没頭する"] },
  { ageRange: "20-24", methods: ["深呼吸エクササイズ", "ジムに通う", "旅行の計画を立てる"] },
  { ageRange: "25-29", methods: ["本を読む", "軽いストレッチ", "音楽や映画を楽しむ"] },
  { ageRange: "30-39", methods: ["ヨガ", "趣味を楽しむ", "仕事から離れて休息を取る"] },
  { ageRange: "40-49", methods: ["家族と過ごす", "庭仕事をする", "料理に挑戦する"] },
  { ageRange: "50-59", methods: ["ウォーキング", "軽いジョギング", "地域活動に参加"] },
  { ageRange: "60+", methods: ["瞑想", "散歩", "クラフトや手芸を楽しむ"] }
];

const App = () => {
  const [age, setAge] = useState(null); // ユーザーの年齢
  const [suggestions, setSuggestions] = useState([]);
  const [isAgeSet, setIsAgeSet] = useState(false); // 年齢が設定されたか確認

  // 初回ロード時に年齢を取得
  useEffect(() => {
    const savedAge = localStorage.getItem("userAge");
    if (savedAge) {
      setAge(parseInt(savedAge, 10));
      setSuggestions(getSuggestions(parseInt(savedAge, 10)));
      setIsAgeSet(true);
    }
  }, []);

  // 年齢に基づいてストレス解消法を取得
  const getSuggestions = (userAge) => {
    const suggestion = stressReliefData.find(({ ageRange }) => {
      const [min, max] = ageRange.split("-").map(Number);
      return max ? userAge >= min && userAge <= max : userAge >= min;
    });
    return suggestion ? suggestion.methods : ["該当するストレス解消法が見つかりません"];
  };

  // 年齢設定時の処理
  const handleSetAge = () => {
    const userAge = parseInt(age, 10);
    if (!isNaN(userAge) && userAge > 0) {
      localStorage.setItem("userAge", userAge); // 年齢をローカルストレージに保存
      setSuggestions(getSuggestions(userAge)); // ストレス解消法を取得
      setIsAgeSet(true); // 年齢設定完了
    } else {
      alert("有効な年齢を入力してください！");
    }
  };

  return (
    <div style={{ padding: "20px", maxWidth: "400px", margin: "0 auto", textAlign: "center" }}>
      <h1>ストレス解消法アプリ</h1>
      {!isAgeSet ? (
        // 年齢入力画面
        <div>
          <h2>はじめに年齢を入力してください</h2>
          <input
            type="number"
            value={age || ""}
            onChange={(e) => setAge(e.target.value)}
            style={{ margin: "10px 0", padding: "5px", fontSize: "16px" }}
            placeholder="例: 25"
          />
          <button
            onClick={handleSetAge}
            style={{ padding: "10px 20px", fontSize: "16px", cursor: "pointer" }}
          >
            年齢を登録
          </button>
        </div>
      ) : (
        // ストレス解消法の表示画面
        <div>
          <h2>おすすめのストレス解消法</h2>
          <ul>
            {suggestions.map((method, index) => (
              <li key={index}>{method}</li>
            ))}
          </ul>
          <button
            onClick={() => {
              setIsAgeSet(false);
              setAge(null);
              localStorage.removeItem("userAge"); // 年齢をリセット
            }}
            style={{ marginTop: "20px", padding: "10px 20px", fontSize: "16px", cursor: "pointer" }}
          >
            年齢を変更
          </button>
        </div>
      )}
    </div>
  );
};

export default App;
