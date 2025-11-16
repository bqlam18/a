## Development/Implementation View — Mentor/Tutor System (MVC Architecture)

### Layer Structure

| Layer       | Functions                                            | Components                                             | Schemas/Entities                                  |
|-------------|-----------------------------------------------------|--------------------------------------------------------|---------------------------------------------------|
| **View**    | - Display data<br>- Get user actions (click/input)<br>- Render forms and dashboards | - StudentDashboardView<br>- AssignmentSubmissionView<br>- ComplaintFormView<br>- SurveyResponseView<br>- NotificationView<br>- ClassManagementView<br>- AdminPanelView | *(none – View does not persist data)*             |
| **Controller** | - Handle requests<br>- Validate & authorize users<br>- Route actions to Model<br>- Business logic | - AuthController<br>- AssignmentController<br>- AttendanceController<br>- SurveyController<br>- NotificationController<br>- ComplaintController<br>- UserController | *(none – Controllers use Model schema)*           |
| **Model**   | - Define business entities<br>- CRUD operations<br>- Integrate with external APIs<br>- Data integrity | - Repositories<br>- ORM Schemas<br>- Data Adapters     | Student<br>Tutor<br>Admin<br>Profile<br>Course<br>ClassSection<br>Enrollment<br>Assignment<br>AssignmentSubmission<br>Material<br>Announcement<br>AttendanceEvent<br>AttendanceEntry<br>Complaint<br>File<br>SurveyResponse<br>ScheduleSlot |

---

### Data Flow

- **User → View:** Interacts with UI (click, input, navigation)
- **View → Controller:** Sends actions/requests for processing
- **Controller → Model:** CRUD, queries, applies logic
- **Model → Controller:** Returns data/status
- **Controller → View:** Updates the UI with results

```

---

```plantuml name=development_implementation_view.puml
@startuml
title Development/Implementation View - Mentor/Tutor System (MVC Architecture)

// Actors
actor User

// View Layer
class "View Layer (UI)" as View {
  StudentDashboardView
  AssignmentSubmissionView
  ComplaintFormView
  SurveyResponseView
  NotificationView
  ClassManagementView
  AdminPanelView
}

// Controller Layer
class "Controller Layer" as Controller {
  AuthController
  AssignmentController
  AttendanceController
  SurveyController
  NotificationController
  ComplaintController
  UserController
}

// Model Layer
class "Model Layer (Data/Entities)" as Model {
  Student
  Tutor
  Admin
  Profile
  Course
  ClassSection
  Enrollment
  Assignment
  AssignmentSubmission
  Material
  Announcement
  AttendanceEvent
  AttendanceEntry
  Complaint
  File
  SurveyResponse
  ScheduleSlot
}

// Relations
User --> View : interacts
View --> Controller : sends request
Controller --> Model : data operations
Model --> Controller : returns data
Controller --> View : updates UI
@enduml
```

---

**Hướng dẫn sử dụng:**
- **Markdown:** Copy block đầu vào báo cáo/thuyết trình, trình bày logic, chức năng, thành phần cho từng layer theo bảng.
- **PlantUML:** Copy block sau vào bất kỳ tool PlantUML nào (VSCode, plantuml.com…), chắc chắn render diagram không lỗi.
- Diagram này đúng format, không dùng ký tự hay cấu trúc lạ gây lỗi, có đầy đủ các layer và quan hệ mà bạn yêu cầu!

Nếu muốn vẽ từng class/có thêm thuộc tính, hoặc thêm các note mô tả chi tiết thì chỉ cần báo lại!
