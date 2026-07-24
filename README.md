CREATE TABLE users (
    id BIGSERIAL PRIMARY KEY, -- Sử dụng BIGSERIAL tự động tăng trong Postgres
    username VARCHAR(50) NOT NULL UNIQUE,
    password VARCHAR(255) NOT NULL, -- Lưu mật khẩu mã hóa Bcrypt
    fullname VARCHAR(100),
    current_streak INT DEFAULT 0,
    longest_streak INT DEFAULT 0,
    last_active_date DATE, -- Kiểu DATE lưu chuẩn YYYY-MM-DD
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP -- Lưu đầy đủ múi giờ
);

-- 2. BẢNG TỪ VỰNG (Kho từ vựng của từng user)
CREATE TABLE vocabularies (
    id BIGSERIAL PRIMARY KEY,
    user_id BIGINT NOT NULL,
    word VARCHAR(100) NOT NULL,
    meaning TEXT NOT NULL,
    language_type VARCHAR(10) NOT NULL, -- 'ENGLISH' hoặc 'KOREAN'
    is_starred BOOLEAN DEFAULT FALSE, -- Sử dụng kiểu BOOLEAN nguyên bản của Postgres
    wrong_count INT DEFAULT 0, -- Tổng số lần làm sai trong lịch sử (Sổ đen)
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    CONSTRAINT fk_user FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
);

-- Tạo các INDEX tối ưu hóa thuật toán bốc đề ngẫu nhiên (RANDOM) theo ngày và mức độ sai trong Postgres
CREATE INDEX idx_vocab_user_date ON vocabularies (user_id, created_at);
CREATE INDEX idx_vocab_user_starred ON vocabularies (user_id, is_starred);
CREATE INDEX idx_vocab_user_wrong ON vocabularies (user_id, wrong_count);

-- 3. BẢNG LỊCH NHẮC NHỞ (Cấu hình giờ quét để Browser Notification gửi thông báo)
CREATE TABLE reminders (
    id BIGSERIAL PRIMARY KEY,
    user_id BIGINT NOT NULL,
    remind_time TIME NOT NULL, -- Kiểu TIME không múi giờ (Ví dụ: '12:00:00')
    is_active BOOLEAN DEFAULT TRUE,
    CONSTRAINT fk_reminder_user FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
);

-- Chèn dữ liệu mẫu cho Anh Phú để test nhanh (mật khẩu dạng thô: "123456")
INSERT INTO users (username, password, fullname, current_streak, longest_streak) 
VALUES ('admin', '123456', 'Anh Phú', 0, 0);





import jakarta.persistence.*;
import lombok.Data;
import java.time.ZonedDateTime;

@Entity
@Table(name = "vocabularies", indexes = {
    @Index(name = "idx_vocab_user_date", columnList = "user_id, created_at"),
    @Index(name = "idx_vocab_user_starred", columnList = "user_id, is_starred"),
    @Index(name = "idx_vocab_user_wrong", columnList = "user_id, wrong_count")
})
@Data // Tự động sinh Getter, Setter, toString bằng Lombok
public class Vocabulary {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(name = "user_id", nullable = false)
    private Long userId;

    @Column(nullable = false, length = 100)
    private String word;

    @Column(nullable = false, columnDefinition = "TEXT")
    private String meaning;

    @Column(name = "language_type", nullable = false, length = 10)
    private String languageType;

    @Column(name = "is_starred")
    private boolean isStarred;

    @Column(name = "wrong_count")
    private int wrongCount;

    @Column(name = "created_at", insertable = false, updatable = false)
    private ZonedDateTime createdAt;

    @Column(name = "updated_at", insertable = false, updatable = false)
    private ZonedDateTime updatedAt;
}
