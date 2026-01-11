## 1. myphpadmin 설치 


### 1단계: MariaDB 데이터베이스 설계 (Schema)

가장 먼저 데이터를 담을 그릇을 만들어야 합니다. 구글 시트의 내용을 DB 테이블로 옮기고, 사용자 기능을 추가합니다.


#### 필요한 테이블 구성안

1. **Users (학생 테이블)**: 아이디, 비번, 이름, 주말 미루기 잔여 횟수
2. **Curriculum (공부 내용 테이블)**: 날짜, 과목, 제목, 영상URL, 포인트 내용 등 (구글 시트 내용)
3. **Progress (학습 기록 테이블)**: '누가', '어떤 강의'를, '언제' 완료했는지, '미뤘는지' 여부

**[SQL 예시 - 바로 사용 가능]**

```sql
-- 1. 학생 테이블
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(50) NOT NULL UNIQUE, -- 로그인 아이디
    password VARCHAR(255) NOT NULL,       -- 암호화된 비밀번호
    name VARCHAR(50) NOT NULL,            -- 학생 이름
    weekend_chance INT DEFAULT 3,         -- 주말 미루기 잔여 횟수 (매주 초기화 필요)
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- 2. 커리큘럼(공부 내용) 테이블 (구글 시트 내용이 여기로 들어감)
CREATE TABLE curriculums (
    id INT AUTO_INCREMENT PRIMARY KEY,
    study_date DATE NOT NULL,             -- 공부할 날짜
    subject VARCHAR(20),                  -- 과목
    title VARCHAR(100),                   -- 제목
    video_url TEXT,                       -- 영상 링크 (유튜브/Vimeo)
    point_title VARCHAR(100),             -- 포인트 제목
    point_content TEXT,                   -- 포인트 내용 (마크다운)
    tip TEXT                              -- 팁
);

-- 3. 학습 현황 테이블 (누가 뭘 했는지)
CREATE TABLE study_progress (
    id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT,                          -- 학생 ID
    curriculum_id INT,                    -- 커리큘럼 ID
    status ENUM('completed', 'deferred') NOT NULL, -- 상태 (완료/미룸)
    completed_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(id),
    FOREIGN KEY (curriculum_id) REFERENCES curriculums(id)
);

```