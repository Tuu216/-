##旅遊景點推薦系統##

問題發現：
旅遊對於現代人來說是一個放鬆的娛樂行為之一，但因為平時課業、工作忙碌，時常無法空出多餘的時間安排旅遊行程，為節省旅遊的前置準備，使旅遊更加輕鬆，因此我們希望開發一套景點推薦系統，讓使用者可以透過這套系統快速的篩選出旅遊目的地來安排行程。
除了景點推薦以外，未來這套系統也規劃可以結合大型語言模型來進行行程上的細節安排，目的在於建立一套足夠簡單可以讓使用者安排出一套滿意旅遊行程的推薦系統。
目標用戶：
一般旅遊者、日常生活較為繁忙者
短期開發任務：旅遊景點推薦系統
長期目標：結合大型語言模型（如 GPT）實現行程細節自動規劃，例如生成完整的旅遊行程表，包含每日景點安排與交通建議。
預計功能需求：
輸入：
- 使用者輸入旅遊目的地 (如：國家、城市)。
- 預計旅遊時間（月/日，非必填）。
- 預計花費（非必填）。
- 旅伴類型（家人、朋友、情侶、無旅伴），進一步細分如是否有小孩或長者。
輸出：
   - 推薦程度（五星等第）。
   - 地址。
   - 營業時間。
   - 推薦理由。
   - 景點關鍵字。
   - 其他人評論（滾輪式滑動）。
   - 價位區間/門票花費。
執行方式：
1.	輸出的景點基本資料由google api抓取：
   使用 Google Places API 提取景點名稱、地址、評分、營業時間、價格層級和評論。
   技術：使用 Python 的 requests 庫進行 API 調用，解析 JSON 數據並存儲至資料庫（如 SQLite 或 PostgreSQL）。
2.	推薦方法採用混和型過濾：
   甲、	基於內容的過濾：
      i.	利用 TF-IDF 方法生成景點特徵向量。
      ii.	計算用戶偏好與景點屬性向量的餘弦相似度。
      iii.	工具：`scikit-learn`。
      iv.	依照使用者的本次搜尋輸入、過去使用記錄、評價及屬性偏好進行推薦。
      v.	推薦權重排序：本次搜尋的輸入 > 使用者過去記錄 > 使用者評價及屬性偏好。
   乙、	協同過濾：
      i.	使用 K-Nearest Neighbors（KNN）演算法計算相似用戶的行為模式。
      ii.	工具：`surprise` 或 `scikit-learn`。
      iii.	建立用戶-景點評分矩陣，進行協同過濾推薦。
3.	活動類型分類架構：
   甲、	Level 1：
      i.	藝術人文、娛樂遊玩、運動休閒、吃貨天堂。
   乙、	Level 2：
      i.	藝術人文：展覽、古蹟、宗教、表演、節慶活動。
      ii.	娛樂遊玩：遊樂園、賭場、手作體驗。
      iii.	運動休閒：自然景觀、水上活動、登山健行、休閒養生。
      iv.	吃貨天堂：餐廳、小吃、飲料冰品。
4.	旅伴分類架構
   甲、	無旅伴。
   乙、	家人（細分為有無小孩或長者）。
   丙、	朋友。
   丁、	情侶。
5.	初步推薦邏輯
Step 1景點篩選：根據使用者輸入的地點和活動類型，篩選符合條件的景點。
Step 2社群熱門趨勢：根據社群媒體標籤數據，推薦近期熱門景點。
Step 3旅行夥伴調整：根據旅伴類型，優先推薦適合該類型的景點（例如，親子友好型景點適合有小孩的家庭）。
Step 4偏好匹配與排序：計算使用者偏好與景點屬性標籤的相似度，並結合協同過濾結果進行推薦排序。
 
目前問題：
1.	我想參考現在比較熱門的社群軟體如instagram作為推薦依據，但我不知道可以如何獲取api、以及這部分該增加在哪一塊系統設計中。
A.	如何獲取 API
   甲、	Instagram Graph API：
      官方提供 Instagram Graph API，可用於獲取公共帳戶數據（如貼文、標籤、熱門內容等）。
      使用條件：
         需註冊 Meta 開發者帳號並建立應用。
         必須取得應用審核通過才能訪問更多的數據範圍（例如追蹤者互動、貼文分析等）。
      可用數據：
         貼文標籤（Hashtags）。
         貼文互動數據（如按讚、評論數）。
         熱門地點標籤（與景點相關的地理標籤）。
B.	如何融入系統設計
   甲、	數據作為內容過濾的一部分：
      i.	利用 Instagram 或 Threads 的貼文標籤來補充景點的關鍵字和推薦理由。例如：
         1.	獲取特定景點的熱門標籤，將其加入景點屬性。
         2.	分析用戶對這些標籤的偏好，提升推薦準確性。
   乙、	添加社群趨勢推薦功能：
      i.	新增模組：「基於社群熱門趨勢的推薦」。
         1.	例：某景點近期在 Instagram 上的相關貼文數量激增，則在系統中提升該景點的推薦排序。
   丙、	用戶個性化推薦：
      i.	如果系統未來支持用戶登錄，可以連接 Instagram 賬號，分析用戶的互動內容（如按讚的地點貼文），並根據偏好進行推薦。	
      2.	因為還在架構階段且我目前沒有資料、訓練資料會推薦我如何產生：
   甲、	使用開源的景點數據集：
       i.	Kaggle 旅遊相關數據集
         1.	TripAdvisor Hotel/Attraction Review Dataset.
         2.	Yelp Open Dataset.
   乙、	結合虛擬數據與開源數據：
      i.	將開源數據作為基礎，結合虛擬數據進行測試。
      ii.	模擬數據生成：
            1.	景點數據集：
                  旅遊網站爬蟲(GOOGLE MAP)
            2.	用戶偏好數據：
fake_users = [
    {
        "user_id": f"user_{i}",
        "history": random.sample(fake_places, random.randint(5, 10)),
        "preferences": random.sample(["自然景觀", "歷史景點", "親子友好", "浪漫"], 2)
    }
    for i in range(1, 51)
]
   丙、	結合爬蟲抓取：
      i.	使用 `BeautifulSoup` 或 `Selenium` 抓取 Google Maps 或其他旅遊網站的公開數據。
         測試與部署計畫：
            1. 單元測試：
               - 測試 API 數據抓取和推薦模組的正確性。
               - 工具：`pytest`。
            2. 系統測試：
               - 模擬多用戶場景，驗證推薦結果的準確性。
            3. 部署：
               - 使用 Docker 容器化應用，確保環境一致性。
               - 部署至雲端（如 Heroku 或 AWS Lambda）。

預期成果：
1. **短期成果**：提供基於 Google API 和混合過濾的景點推薦功能。
2. **中期成果**：整合 Instagram 數據，提高推薦的熱門性和實時性。
3. **長期成果**：結合 GPT 等大型語言模型，實現完整行程規劃和用戶高度個性化推薦。
