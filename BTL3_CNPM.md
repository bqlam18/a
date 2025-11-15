# Class Diagram — Tutor/Mentor System

## 1. Domain Classes

```mermaid
classDiagram
  direction LR

  %% Core Actor
  class Student {
    +studentId: String
    +name: String
    +email: String
    +profile: Profile
    +enrollments: List~Enrollment~
    +submissions: List~AssignmentSubmission~
    +getEnrolledCourses(): List~CourseSection~
    +getProgress(courseSectionId): ProgressReport
    +submitAssignment(assignmentId, file): SubmissionResult
  }

  class Tutor {
    +tutorId: String
    +name: String
    +email: String
    +profile: Profile
    +courses: List~CourseSection~
    +availability: List~Availability~
    +feedback: List~SurveyResponse~
    +complaints: List~Complaint~
    +manageAssignment(assignmentId): void
    +gradeAssignment(submissionId, score, comment): void
    +makeAnnouncement(courseSectionId, content): void
  }

  class Admin {
    +adminId: String
    +name: String
    +email: String
    +profile: Profile
    +manageSystem(): void
  }

  class Profile {
    +profileId: String
    +role: Role
    +bio: String
    +avatarUrl: String
    +updateProfile(data): void
  }

  %% Core Entities
  class Course {
    +courseId: String
    +courseName: String
    +faculty: String
    +sections: List~CourseSection~
    +materials: List~Material~
    +assignments: List~Assignment~
  }

  class CourseSection {
    +sectionId: String
    +courseId: String
    +term: String
    +tutorId: String
    +students: List~Student~
    +schedule: List~ScheduleSlot~
    +assignments: List~Assignment~
    +announcements: List~Announcement~
    +attendanceEvents: List~AttendanceEvent~
    +getProgressReport(): ProgressReport
  }

  class Enrollment {
    +enrollmentId: String
    +studentId: String
    +sectionId: String
    +status: EnrollmentStatus
    +enrolledAt: DateTime
  }

  class Material {
    +materialId: String
    +title: String
    +type: MaterialType
    +importance: MaterialImportance
    +description: String
    +fileUrl: String
    +version: String
    +addedBy: String
    +addedAt: DateTime
    +updateMetadata(data): void
  }

  class Assignment {
    +assignmentId: String
    +sectionId: String
    +title: String
    +description: String
    +dueDate: DateTime
    +type: AssignmentType
    +maxScore: float
    +createdBy: String
    +createdAt: DateTime
    +updateAssignment(data): void
  }

  class AssignmentSubmission {
    +submissionId: String
    +assignmentId: String
    +studentId: String
    +fileUrl: String
    +submittedAt: DateTime
    +score: float
    +feedback: String
    +status: SubmissionStatus
    +grade(score, feedback): void
  }

  %% Progress & Score
  class ProgressReport {
    +studentId: String
    +sectionId: String
    +completion: float
    +avgScore: float
    +attendance: int
    +lastUpdated: DateTime
  }

  %% Announcements & Notification
  class Announcement {
    +announcementId: String
    +sectionId: String
    +content: String
    +createdBy: String
    +timestamp: DateTime
    +visibleUntil: DateTime
  }

  class Notification {
    +notificationId: String
    +userId: String
    +type: NotificationType
    +content: String
    +createdAt: DateTime
    +read: Boolean
    +markAsRead(): void
  }

  %% Attendance
  class AttendanceEvent {
    +attendanceId: String
    +sectionId: String
    +date: DateTime
    +openedBy: String
    +lateThreshold: DateTime
    +attendanceList: List~AttendanceEntry~
    +openAttendance(): void
    +closeAttendance(): void
  }

  class AttendanceEntry {
    +attendanceEntryId: String
    +attendanceId: String
    +studentId: String
    +status: AttendanceStatus
    +timestamp: DateTime
  }

  %% Survey & Complaint
  class SurveyResponse {
    +responseId: String
    +studentId: String
    +sectionId: String
    +formData: List~SurveyAnswer~
    +submittedAt: DateTime
  }

  class SurveyAnswer {
    +questionId: String
    +answer: String
    +score: int
  }

  class Complaint {
    +complaintId: String
    +byUserId: String
    +sectionId: String
    +relatedData: String
    +content: String
    +files: List~File~
    +status: ComplaintStatus
    +response: String
    +createdAt: DateTime
    +resolvedAt: DateTime
  }

  class File {
    +fileId: String
    +fileName: String
    +fileType: String
    +fileUrl: String
    +size: int
    +uploadedAt: DateTime
  }

%% Integration Adapters
  class DataCoreClient {
    +getCourses(studentId): List~Course~
    +getSections(tutorId): List~CourseSection~
    +getStudentProfile(userId): Profile
    +registerCourse(studentId, sectionId): RegistrationResult
    +getSchedule(studentId|tutorId): List~ScheduleSlot~
    +getGrades(userId): List~AssignmentSubmission~
    +getAttendance(sectionId): List~AttendanceEvent~
  }

  class LibraryClient {
    +searchMaterials(courseId): List~Material~
    +getMaterial(materialId): Material
  }

  class SSOClient {
    +authenticate(account, password): AuthResult
    +getRoles(userId): List~Role~
    +syncUserProfile(userId): Profile
  }

  class NotificationService {
    +sendNotification(userId, content): NotificationResult
    +getUnreadNotifications(userId): List~Notification~
  }

  class SurveyService {
    +getSurvey(sectionId): List~SurveyForm~
    +submitSurvey(studentId, sectionId, formData): SurveyResult
    +getSurveyResults(sectionId): List~SurveyResponse~
  }

%% ENUMS
  enum Role {
    STUDENT
    TUTOR
    ADMIN
    OFFICER
  }
  enum EnrollmentStatus {
    ENROLLED
    DROPPED
    COMPLETED
    WAITLIST
  }
  enum AssignmentType {
    QUIZ
    HOMEWORK
    PROJECT
  }
  enum SubmissionStatus {
    SUBMITTED
    GRADED
    LATE
    REJECTED
  }
  enum MaterialType {
    SYLLABUS
    SLIDE
    TEXTBOOK
    EXERCISE
    ANNOUNCEMENT
    OTHER
  }
  enum MaterialImportance {
    REQUIRED
    OPTIONAL
  }
  enum AttendanceStatus {
    PRESENT
    LATE
    ABSENT
  }
  enum NotificationType {
    DEADLINE
    ANNOUNCEMENT
    EVENT
    FEEDBACK
    SYSTEM
  }
  enum ComplaintStatus {
    OPEN
    IN_PROGRESS
    RESOLVED
    REJECTED
  }

%% RELATIONSHIPS
  Student "1" --> "*" Enrollment : enrolls
  Tutor "1" --> "*" CourseSection : teaches
  Course "1" --> "*" CourseSection : contains
  CourseSection "*" --> "1" Tutor : taught by
  CourseSection "*" --> "*" Student : hasStudent
  CourseSection "1" --> "*" Announcement : hasAnnouncements
  CourseSection "1" --> "*" AttendanceEvent : hasAttendance
  AttendanceEvent "1" --> "*" AttendanceEntry : attendanceOf
  Assignment "1" --> "*" AssignmentSubmission : hasSubmission
  Student "1" --> "*" AssignmentSubmission : submits
  CourseSection "1" --> "*" Assignment : hasAssignment
  CourseSection "1" --> "*" Material : hasMaterial
  AssignmentSubmission "*" --> "1" Assignment : belongsTo
  Student "1" --> "*" SurveyResponse : sends
  Tutor "1" --> "*" SurveyResponse : receives
  Complaint "1" --> "1" CourseSection : relatesTo
  Complaint "*" --> "1" File : hasAttachment
```

---

## 2. Method List & Description (Domain + Services + Adapter)

| **Class/Service**              | **Method/Signature**                                                        | **Purpose / Description**                                                                                                                                          | **UC Mapping**                                    | **Error/Notes**                                      |
|-----------------------------|--------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------|------------------------------------------------------|
| **Student**                | getEnrolledCourses()                                                      | Trả về danh sách các môn/lớp sinh viên đang học                                                                                                                  | UC21, UC20                                        | 404 nếu không có Enrollment                          |
|                             | getProgress(courseSectionId)                                             | Lấy tiến độ học tập, điểm trung bình, số lần điểm danh etc cho từng môn                                                    | UC10, UC24, UC44                                  |                                                      |
|                             | submitAssignment(assignmentId, file)                                     | Nộp bài tập, kiểm tra thời hạn, định dạng, lưu file submission.                                                            | UC15, UC39                                        | 403 nếu hết hạn; 400 nếu sai format                  |
| **Tutor**                   | manageAssignment(assignmentId)                                           | Thêm/sửa/xóa bài tập, lọc bài nộp, xem thông tin chi tiết                                                                  | UC39, UC38                                        | 404 nếu không tồn tại; 403 nếu không có quyền        |
|                             | gradeAssignment(submissionId, score, comment)                            | Chấm điểm & phản hồi cho sinh viên                                                                                         | UC39                                              | 404 nếu submission không tồn tại                     |
|                             | makeAnnouncement(sectionId, content)                                     | Tạo thông báo cho lớp/môn                                                                                                  | UC32, UC22                                        | Rate-limit nếu spam                                  |
|                             | replyComplaint(complaintId, response)                                    | Phản hồi khiếu nại từ SV                                                                                                   | UC17, UC30                                        | Log/Audit                                            |
| **Admin**                   | manageSystem()                                                           | Quản trị hệ thống                                                                                                          | UC37                                              | Chỉ cho role ADMIN                                   |
| **Enrollment**              | enroll(studentId, sectionId)                                             | Đăng ký lớp cho sinh viên                                                                                                  | UC27, UC9                                         | Check prerequisite, time conflict, chỗ trống         |
| **AssignmentSubmission**    | grade(score, feedback)                                                   | Ghi điểm và nhận xét cho bài nộp                                                                                          | UC39                                              |                                                      |
| **Announcement**            | markAsRead()                                                             | Đánh dấu thông báo đã đọc                                                                                                  | UC16, UC22                                        |                                                      |
| **AttendanceEvent**         | openAttendance()                                                         | Mở sự kiện điểm danh                                                                                                       | UC25, UC47                                        | Chỉ Tutor/GV                                         |
|                             | closeAttendance()                                                        | Đóng sự kiện điểm danh                                                                                                     | UC47                                              |                                                      |
| **AttendanceEntry**         | updateStatus(status)                                                     | Điều chỉnh trạng thái điểm danh                                                                                           | UC47                                              |                                                      |
| **SurveyResponse**          | submitSurvey(sectionId, formData)                                        | Sinh viên gửi survey/feedback môn học                                                                                      | UC18, UC8, UC31, UC40                             | Chỉ 1 lần/môn/1 SV                                   |
| **Complaint**               | submitComplaint(byUserId, sectionId, content, files)                     | Tạo khiếu nại mới                                                                                                          | UC17, UC30, UC36                                  | 400 thiếu dữ liệu                                    |
| **DataCoreClient**          | getCourses(studentId)                                                    | Truy vấn danh sách môn/lớp theo SV                                                                                         | UC21, UC20, UC41, UC45                            | Integration error                                    |
|                             | registerCourse(studentId, sectionId)                                     | Đăng ký môn học kỳ sau                                                                                                     | UC27, UC42                                        | Race, conflict, check rule                           |
|                             | getGrades(userId)                                                        | Lấy bảng điểm                                                                                                              | UC24, UC38, UC44                                  |                                                      |
|                             | getAttendance(sectionId)                                                 | Lấy thông tin điểm danh từng buổi                                                                                         | UC25, UC47                                        |                                                      |
| **LibraryClient**           | searchMaterials(courseId)                                                | Truy vấn tài liệu từ thư viện                                                                                              | UC19, UC5, UC4                                    | 504 nếu timeout; 404 nếu không có                    |
|                             | getMaterial(materialId)                                                  | Lấy metadata chi tiết cho tài liệu                                                                                         | UC19                                              |                                                      |
| **SSOClient**               | authenticate(account, password)                                          | Đăng nhập xác thực                                                                   | UC6, UC33                                         | 401/403                                              |
|                             | getRoles(userId)                                                         | Trả danh sách quyền của user                                                         | UC6, UC33, UC34                                   |                                                      |
| **NotificationService**     | sendNotification(userId, content)                                        | Gửi thông báo đến user (SV/Tutor/Staff/Admin)                                        | UC22, UC23, UC32, UC43                            |                                                      |
|                             | getUnreadNotifications(userId)                                           | Lấy danh sách thông báo chưa đọc                                                      | UC16, UC22                                        |                                                      |
| **SurveyService**           | getSurvey(sectionId)                                                     | Lấy form khảo sát cho từng môn/lớp                                                   | UC18, UC8, UC31, UC40                             |                                                      |
|                             | submitSurvey(studentId, sectionId, formData)                             | Lưu khảo sát, kiểm tra hợp lệ                                                        | UC18                                              | 400 thiếu bắt buộc, 409 đã tồn tại                   |
|                             | getSurveyResults(sectionId)                                              | Lấy thống kê khảo sát với môn/lớp                                                    | UC8, UC31, UC40                                   |                                                      |


---

**Ghi chú:**  
- Tất cả class/method đều có thể được mapping với sequence diagram bạn đã gửi, hoặc có cùng logic: actor → UI/Form → Controller → Service/Adapter → Database hoặc hệ thống tích hợp (DATACORE, LIBRARY, SSO).
- Nếu có entity nào cần tách nhỏ/thêm field, bạn cứ bổ sung để diagram chi tiết hơn.

---

## Next steps

- Nếu cần chia nhỏ diagram cho từng module: Student, Tutor, Admin; mình có thể tách lại.
- Nếu muốn export thêm bản PlantUML, PDF, hoặc README mapping diagram ↔ sequence diagram, báo lại nhé!
- Nếu nhóm bạn có review, sửa lại entity/field/method nào bạn gửi, cứ gửi lại để mình cập nhật bản cuối!

Bạn chỉ việc copy và dùng file trên, mapping cho Submission #3, đã khớp với toàn bộ UC và sequence diagram bạn đã cung cấp!
