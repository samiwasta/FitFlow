---
name: Gym Dashboard Production Setup
overview: Create a production-ready subscription-based gym dashboard with Better Auth role-based authentication, Supabase database, Stripe subscriptions, workout planner, and AI-powered diet planner using Google Gemini API.
todos:
  - id: setup-nextjs
    content: Initialize Next.js 15 project with TypeScript, Tailwind CSS, and shadcn/ui components
    status: in_progress
  - id: setup-env
    content: Configure environment variables for Supabase, Better Auth, Stripe, and Google Gemini API
    status: pending
    dependencies:
      - setup-nextjs
  - id: setup-supabase
    content: Create Supabase project and database schema with tables for users, roles, subscriptions, workouts, and diet plans
    status: pending
    dependencies:
      - setup-env
  - id: setup-better-auth
    content: Configure Better Auth with Supabase adapter and implement role-based authentication
    status: pending
    dependencies:
      - setup-supabase
  - id: create-middleware
    content: Implement middleware for route protection and role-based redirects
    status: pending
    dependencies:
      - setup-better-auth
  - id: setup-stripe
    content: Integrate Stripe for subscription management with checkout flow and webhooks
    status: pending
    dependencies:
      - setup-better-auth
  - id: build-workout-planner
    content: Create workout planner with exercise library, plan builder, and progress tracking
    status: pending
    dependencies:
      - create-middleware
  - id: integrate-gemini
    content: Set up Google Gemini API integration for AI-powered diet plan generation
    status: pending
    dependencies:
      - create-middleware
  - id: build-diet-planner
    content: Create diet planner UI with meal plans, nutrition tracking, and shopping lists
    status: pending
    dependencies:
      - integrate-gemini
  - id: create-dashboards
    content: Build role-based dashboards for Admin, Member, Trainer, and Staff with appropriate features
    status: pending
    dependencies:
      - build-workout-planner
      - build-diet-planner
  - id: add-error-handling
    content: Implement error handling, validation, and user feedback mechanisms
    status: pending
    dependencies:
      - create-dashboards
  - id: production-optimization
    content: Add security measures, performance optimization, and prepare for deployment
    status: pending
    dependencies:
      - add-error-handling
---

# Production-Ready Gym Dashboard with Next.js

## Architecture Overview

The application will be built using Next.js 15 with App Router, featuring:

- **Authentication**: Better Auth with role-based access control (Admin, Member, Trainer, Staff)
- **Database**: Supabase (PostgreSQL) for data persistence
- **Payments**: Stripe for subscription management
- **AI Integration**: Google Gemini API for diet planning
- **UI**: Tailwind CSS with shadcn/ui components

## Role-Based Feature Summary

### Admin (Full System Access)

- Complete user, membership, and staff management
- Financial management and reporting
- System configuration and analytics
- Class and facility management
- Comprehensive reporting dashboard

### Member (End User)

- Personal profile and membership management
- Workout planner and tracking
- AI-powered diet planner
- Class booking and personal training
- Progress tracking and achievements
- Payment and billing management

### Trainer (Client Management)

- Client management and profiles
- Workout and diet plan creation
- Session scheduling and management
- Progress monitoring and reporting
- Client communication
- Class management

### Staff (Front Desk Operations)

- Member check-in/check-out
- Membership enrollment assistance
- Payment processing
- Class scheduling support
- Member support and inquiries
- Daily operations management

## System Flowcharts

### 1. Authentication & Role-Based Access Flow

```mermaid
flowchart TD
    Start([User Visits App]) --> CheckAuth{Authenticated?}
    CheckAuth -->|No| LoginPage["Login/Register Page"]
    CheckAuth -->|Yes| GetUserRole["Get User Role from Session"]
    
    LoginPage --> UserLogin["User Enters Credentials"]
    UserLogin --> BetterAuth["Better Auth Validates"]
    BetterAuth -->|Invalid| ShowError["Show Error Message"]
    ShowError --> LoginPage
    BetterAuth -->|Valid| CreateSession["Create Session"]
    CreateSession --> GetUserRole
    
    GetUserRole --> CheckRole{"User Role?"}
    CheckRole -->|Admin| AdminDashboard["Admin Dashboard"]
    CheckRole -->|Member| MemberDashboard["Member Dashboard"]
    CheckRole -->|Trainer| TrainerDashboard["Trainer Dashboard"]
    CheckRole -->|Staff| StaffDashboard["Staff Dashboard"]
    
    AdminDashboard --> AdminFeatures["Admin Features Access"]
    MemberDashboard --> MemberFeatures["Member Features Access"]
    TrainerDashboard --> TrainerFeatures["Trainer Features Access"]
    StaffDashboard --> StaffFeatures["Staff Features Access"]
    
    AdminFeatures --> MiddlewareCheck{"Protected Route?"}
    MemberFeatures --> MiddlewareCheck
    TrainerFeatures --> MiddlewareCheck
    StaffFeatures --> MiddlewareCheck
    
    MiddlewareCheck -->|Authorized| AllowAccess["Allow Access"]
    MiddlewareCheck -->|Unauthorized| Redirect403["Redirect to 403 or Home"]
    
    AllowAccess --> End(["User Access Granted"])
    Redirect403 --> End
```



### 2. Member User Journey Flow

```mermaid
flowchart TD
    MemberLogin([Member Logs In]) --> MemberDashboard[Member Dashboard]
    
    MemberDashboard --> DashboardOptions{Select Action}
    
    DashboardOptions -->|View Profile| ProfilePage[Profile Management]
    DashboardOptions -->|Workouts| WorkoutSection[Workout Planner]
    DashboardOptions -->|Diet| DietSection[Diet Planner]
    DashboardOptions -->|Classes| ClassBooking[Class Booking]
    DashboardOptions -->|Progress| ProgressTracking[Progress Tracking]
    DashboardOptions -->|Payments| PaymentSection[Payment Management]
    
    ProfilePage --> UpdateProfile[Update Personal Info]
    UpdateProfile --> SaveToDB[(Supabase Database)]
    
    WorkoutSection --> WorkoutOptions{Workout Action}
    WorkoutOptions -->|View Plans| ViewWorkoutPlans[View Assigned Plans]
    WorkoutOptions -->|Create Custom| CreateWorkout[Create Custom Workout]
    WorkoutOptions -->|Log Session| LogWorkout[Log Workout Session]
    
    ViewWorkoutPlans --> DisplayExercises[Display Exercises & Schedule]
    CreateWorkout --> ExerciseLibrary[Browse Exercise Library]
    ExerciseLibrary --> AddExercises[Add Exercises to Plan]
    AddExercises --> SaveWorkoutPlan[Save Workout Plan]
    LogWorkout --> RecordSets[Record Sets, Reps, Weight]
    RecordSets --> SaveWorkoutLog[(Save to Database)]
    
    DietSection --> DietOptions{Diet Action}
    DietOptions -->|Generate Plan| GenerateDiet[AI Diet Plan Generation]
    DietOptions -->|View Plan| ViewDietPlan[View Current Diet Plan]
    DietOptions -->|Log Meals| LogMeals[Log Daily Meals]
    
    GenerateDiet --> InputPreferences[Input Preferences & Goals]
    InputPreferences --> CallGeminiAPI[Call Google Gemini API]
    CallGeminiAPI --> GenerateMealPlan[Generate Personalized Meal Plan]
    GenerateMealPlan --> SaveDietPlan[(Save to Database)]
    ViewDietPlan --> DisplayMeals[Display Meal Calendar]
    LogMeals --> RecordNutrition[Record Nutrition Data]
    RecordNutrition --> SaveNutritionLog[(Save to Database)]
    
    ClassBooking --> BrowseClasses[Browse Available Classes]
    BrowseClasses --> SelectClass[Select Class]
    SelectClass --> CheckCapacity{Class Full?}
    CheckCapacity -->|Yes| JoinWaitlist[Join Waitlist]
    CheckCapacity -->|No| BookClass[Book Class]
    BookClass --> SaveBooking[(Save Booking)]
    JoinWaitlist --> SaveWaitlist[(Save to Waitlist)]
    
    ProgressTracking --> ViewProgress[View Progress Charts]
    ViewProgress --> LogMeasurements[Log Body Measurements]
    LogMeasurements --> SaveProgress[(Save Progress Data)]
    
    PaymentSection --> ViewSubscription[View Subscription Details]
    ViewSubscription --> PaymentActions{Payment Action}
    PaymentActions -->|Renew| RenewMembership[Renew Membership]
    PaymentActions -->|Upgrade| UpgradePlan[Upgrade Plan]
    PaymentActions -->|View History| ViewBilling[View Billing History]
    
    RenewMembership --> StripeCheckout[Stripe Checkout]
    UpgradePlan --> StripeCheckout
    StripeCheckout --> ProcessPayment[Process Payment]
    ProcessPayment --> UpdateSubscription[(Update Subscription)]
    
    SaveToDB --> End([Action Complete])
    SaveWorkoutLog --> End
    SaveDietPlan --> End
    SaveNutritionLog --> End
    SaveBooking --> End
    SaveWaitlist --> End
    SaveProgress --> End
    UpdateSubscription --> End
```



### 3. Subscription Management Flow

```mermaid
flowchart TD
    StartSubscription([User Initiates Subscription]) --> SelectPlan[User Selects Plan]
    SelectPlan --> CheckAuth{User Authenticated?}
    
    CheckAuth -->|No| RedirectLogin[Redirect to Login]
    RedirectLogin --> UserLogin[User Logs In]
    UserLogin --> CheckAuth
    
    CheckAuth -->|Yes| PlanDetails[Display Plan Details]
    PlanDetails --> UserConfirms{User Confirms?}
    UserConfirms -->|No| Cancel[Cancel Subscription]
    UserConfirms -->|Yes| CreateStripeSession[Create Stripe Checkout Session]
    
    CreateStripeSession --> RedirectStripe[Redirect to Stripe Checkout]
    RedirectStripe --> UserPays{Payment Successful?}
    
    UserPays -->|No| PaymentFailed[Payment Failed]
    PaymentFailed --> ShowError[Show Error Message]
    ShowError --> PlanDetails
    
    UserPays -->|Yes| StripeWebhook[Stripe Sends Webhook]
    StripeWebhook --> VerifyWebhook[Verify Webhook Signature]
    VerifyWebhook -->|Invalid| RejectWebhook[Reject Webhook]
    VerifyWebhook -->|Valid| ProcessWebhook[Process Webhook Event]
    
    ProcessWebhook --> WebhookType{Event Type?}
    WebhookType -->|checkout.session.completed| CreateSubscription[Create Subscription Record]
    WebhookType -->|customer.subscription.updated| UpdateSubscription[Update Subscription]
    WebhookType -->|customer.subscription.deleted| CancelSubscription[Cancel Subscription]
    WebhookType -->|invoice.payment_succeeded| RecordPayment[Record Payment]
    WebhookType -->|invoice.payment_failed| HandleFailedPayment[Handle Failed Payment]
    
    CreateSubscription --> SaveToDB[(Save to Supabase)]
    UpdateSubscription --> SaveToDB
    CancelSubscription --> SaveToDB
    RecordPayment --> SaveToDB
    HandleFailedPayment --> NotifyUser[Notify User]
    NotifyUser --> SaveToDB
    
    SaveToDB --> UpdateUserStatus[Update User Subscription Status]
    UpdateUserStatus --> GrantAccess[Grant Feature Access]
    GrantAccess --> SendConfirmation[Send Confirmation Email]
    SendConfirmation --> End([Subscription Active])
    
    Cancel --> End
    RejectWebhook --> End
```



### 4. AI Diet Planner Flow

```mermaid
flowchart TD
    StartDiet([User Accesses Diet Planner]) --> CheckRole{User Role?}
    
    CheckRole -->|Member| MemberDietFlow[Member Diet Flow]
    CheckRole -->|Trainer| TrainerDietFlow[Trainer Diet Flow]
    
    MemberDietFlow --> MemberDietOptions{Member Action}
    MemberDietOptions -->|Generate Plan| GenerateAIDiet[Generate AI Diet Plan]
    MemberDietOptions -->|View Plan| ViewDietPlan[View Current Diet Plan]
    MemberDietOptions -->|Log Meals| LogMeals[Log Daily Meals]
    
    GenerateAIDiet --> InputForm[Input Form]
    InputForm --> CollectData[Collect User Data]
    CollectData --> UserGoals[Fitness Goals]
    CollectData --> DietaryRestrictions[Dietary Restrictions]
    CollectData --> FoodPreferences[Food Preferences]
    CollectData --> Allergies[Allergies]
    CollectData --> ActivityLevel[Activity Level]
    CollectData --> BodyMetrics[Body Metrics]
    
    UserGoals --> ValidateInput[Validate Input]
    DietaryRestrictions --> ValidateInput
    FoodPreferences --> ValidateInput
    Allergies --> ValidateInput
    ActivityLevel --> ValidateInput
    BodyMetrics --> ValidateInput
    
    ValidateInput -->|Invalid| ShowValidationError[Show Validation Errors]
    ShowValidationError --> InputForm
    ValidateInput -->|Valid| PreparePrompt[Prepare AI Prompt]
    
    PreparePrompt --> CallGeminiAPI[Call Google Gemini API]
    CallGeminiAPI --> APIResponse{API Response?}
    
    APIResponse -->|Error| HandleAPIError[Handle API Error]
    HandleAPIError --> Retry{Retry?}
    Retry -->|Yes| CallGeminiAPI
    Retry -->|No| ShowError[Show Error to User]
    ShowError --> End([Process Complete])
    
    APIResponse -->|Success| ParseResponse[Parse AI Response]
    ParseResponse --> ExtractMealPlan[Extract Meal Plan Data]
    ExtractMealPlan --> StructureMeals[Structure Meals by Day]
    StructureMeals --> CalculateNutrition[Calculate Nutrition Values]
    CalculateNutrition --> ValidatePlan{Plan Valid?}
    
    ValidatePlan -->|No| Regenerate[Regenerate Plan]
    Regenerate --> CallGeminiAPI
    ValidatePlan -->|Yes| SaveDietPlan[(Save Diet Plan)]
    SaveDietPlan --> DisplayPlan[Display Generated Plan]
    DisplayPlan --> UserReview{User Approves?}
    
    UserReview -->|No| RequestChanges[Request Changes]
    RequestChanges --> ModifyPrompt[Modify Prompt]
    ModifyPrompt --> CallGeminiAPI
    UserReview -->|Yes| ActivatePlan[Activate Diet Plan]
    ActivatePlan --> NotifyUser[Notify User]
    NotifyUser --> End
    
    ViewDietPlan --> FetchPlan[(Fetch Diet Plan)]
    FetchPlan --> DisplayMealCalendar[Display Meal Calendar]
    DisplayMealCalendar --> ViewMealDetails[View Meal Details]
    ViewMealDetails --> ViewRecipe[View Recipe & Instructions]
    ViewRecipe --> GenerateShoppingList[Generate Shopping List]
    GenerateShoppingList --> End
    
    LogMeals --> SelectDate[Select Date]
    SelectDate --> LogMealType[Select Meal Type]
    LogMealType --> SearchFood[Search Food Database]
    SearchFood --> SelectFood[Select Food Item]
    SelectFood --> EnterQuantity[Enter Quantity]
    EnterQuantity --> SaveMealLog[(Save Meal Log)]
    SaveMealLog --> UpdateNutrition[(Update Daily Nutrition)]
    UpdateNutrition --> End
    
    TrainerDietFlow --> TrainerDietOptions{Trainer Action}
    TrainerDietOptions -->|Create for Client| CreateClientDiet[Create Diet for Client]
    TrainerDietOptions -->|View Client Diet| ViewClientDiet[View Client Diet]
    
    CreateClientDiet --> SelectClientDiet[Select Client]
    SelectClientDiet --> ViewClientProfile[View Client Profile]
    ViewClientProfile --> ChooseMethod{Diet Creation Method}
    ChooseMethod -->|AI Generated| GenerateClientDiet[Generate with AI]
    ChooseMethod -->|Manual| ManualDietCreation[Create Manually]
    
    GenerateClientDiet --> UseClientData[Use Client Data]
    UseClientData --> CallGeminiAPI
    CallGeminiAPI --> ReviewGeneratedPlan[Review Generated Plan]
    ReviewGeneratedPlan --> TrainerApproval{Trainer Approves?}
    TrainerApproval -->|No| EditPlan[Edit Plan]
    TrainerApproval -->|Yes| AssignToClient[Assign to Client]
    EditPlan --> AssignToClient
    
    ManualDietCreation --> AddMealsManually[Add Meals Manually]
    AddMealsManually --> SetNutritionTargets[Set Nutrition Targets]
    SetNutritionTargets --> AssignToClient
    
    AssignToClient --> SaveClientDiet[(Save Client Diet)]
    SaveClientDiet --> NotifyClientDiet[Notify Client]
    NotifyClientDiet --> End
```



### 5. Trainer User Journey Flow

```mermaid
flowchart TD
    TrainerLogin([Trainer Logs In]) --> TrainerDashboard[Trainer Dashboard]
    
    TrainerDashboard --> TrainerOptions{Select Action}
    
    TrainerOptions -->|Clients| ClientManagement[Client Management]
    TrainerOptions -->|Workouts| CreateWorkoutPlans[Create Workout Plans]
    TrainerOptions -->|Diet| CreateDietPlans[Create Diet Plans]
    TrainerOptions -->|Sessions| SessionManagement[Session Management]
    TrainerOptions -->|Classes| ClassManagement[Class Management]
    TrainerOptions -->|Progress| MonitorProgress[Monitor Client Progress]
    
    ClientManagement --> ViewClients[View Assigned Clients]
    ViewClients --> SelectClient[Select Client]
    SelectClient --> ClientProfile[View Client Profile]
    ClientProfile --> ClientActions{Client Action}
    ClientActions -->|View Progress| ViewClientProgress[View Client Progress]
    ClientActions -->|Message| SendMessage[Send Message to Client]
    ClientActions -->|Assign Plan| AssignPlan[Assign Workout/Diet Plan]
    
    CreateWorkoutPlans --> SelectClientForPlan[Select Client]
    SelectClientForPlan --> BuildWorkout[Build Workout Plan]
    BuildWorkout --> AddExercises[Add Exercises & Sets]
    AddExercises --> SetSchedule[Set Workout Schedule]
    SetSchedule --> AssignToClient[Assign to Client]
    AssignToClient --> SaveWorkoutPlan[(Save Workout Plan)]
    SaveWorkoutPlan --> NotifyClient[Notify Client]
    
    CreateDietPlans --> SelectClientForDiet[Select Client]
    SelectClientForDiet --> DietPlanOptions{Diet Plan Type}
    DietPlanOptions -->|Manual| ManualDietPlan[Create Manual Diet Plan]
    DietPlanOptions -->|AI Generated| AIDietPlan[Generate with AI]
    
    ManualDietPlan --> AddMeals[Add Meals & Recipes]
    AddMeals --> SetNutritionGoals[Set Nutrition Goals]
    SetNutritionGoals --> AssignDietPlan[Assign to Client]
    
    AIDietPlan --> InputClientData[Input Client Data & Preferences]
    InputClientData --> CallGeminiAPI[Call Google Gemini API]
    CallGeminiAPI --> GenerateDietPlan[Generate Diet Plan]
    GenerateDietPlan --> ReviewPlan[Review Generated Plan]
    ReviewPlan -->|Approve| AssignDietPlan
    ReviewPlan -->|Edit| EditAIPlan[Edit Plan]
    EditAIPlan --> AssignDietPlan
    
    AssignDietPlan --> SaveDietPlan[(Save Diet Plan)]
    SaveDietPlan --> NotifyClient
    
    SessionManagement --> ViewSchedule[View Training Schedule]
    ViewSchedule --> ScheduleActions{Session Action}
    ScheduleActions -->|Create| CreateSession[Create New Session]
    ScheduleActions -->|Edit| EditSession[Edit Session]
    ScheduleActions -->|Cancel| CancelSession[Cancel Session]
    
    CreateSession --> SelectClientSession[Select Client]
    SelectClientSession --> SetDateTime[Set Date & Time]
    SetDateTime --> SaveSession[(Save Session)]
    SaveSession --> NotifyClient
    
    ClassManagement --> ViewClasses[View Assigned Classes]
    ViewClasses --> ClassActions{Class Action}
    ClassActions -->|Take Attendance| TakeAttendance[Take Class Attendance]
    ClassActions -->|View Participants| ViewParticipants[View Participants]
    ClassActions -->|Manage Schedule| ManageClassSchedule[Manage Schedule]
    
    TakeAttendance --> MarkAttendance[Mark Members Present]
    MarkAttendance --> SaveAttendance[(Save Attendance)]
    
    MonitorProgress --> SelectClientProgress[Select Client]
    SelectClientProgress --> ViewProgressData[View Progress Data]
    ViewProgressData --> ProgressActions{Progress Action}
    ProgressActions -->|Add Notes| AddProgressNotes[Add Progress Notes]
    ProgressActions -->|Generate Report| GenerateReport[Generate Progress Report]
    ProgressActions -->|Update Goals| UpdateGoals[Update Client Goals]
    
    SaveWorkoutPlan --> End([Action Complete])
    SaveDietPlan --> End
    SaveSession --> End
    SaveAttendance --> End
    NotifyClient --> End
```



### 6. Admin User Journey Flow

```mermaid
flowchart TD
    AdminLogin([Admin Logs In]) --> AdminDashboard[Admin Dashboard]
    
    AdminDashboard --> AdminOptions{Select Management Area}
    
    AdminOptions -->|Users| UserManagement[User Management]
    AdminOptions -->|Memberships| MembershipManagement[Membership Management]
    AdminOptions -->|Financial| FinancialManagement[Financial Management]
    AdminOptions -->|Staff| StaffManagement[Staff Management]
    AdminOptions -->|Classes| ClassManagement[Class Management]
    AdminOptions -->|Facility| FacilityManagement[Facility Management]
    AdminOptions -->|Reports| ReportsAnalytics[Reports & Analytics]
    AdminOptions -->|Settings| SystemSettings[System Settings]
    
    UserManagement --> UserActions{User Action}
    UserActions -->|View All| ViewAllUsers[View All Users]
    UserActions -->|Create| CreateUser[Create New User]
    UserActions -->|Edit| EditUser[Edit User]
    UserActions -->|Delete| DeleteUser[Delete User]
    UserActions -->|Assign Role| AssignRole[Assign User Role]
    
    ViewAllUsers --> FilterUsers[Filter by Role/Status]
    CreateUser --> EnterUserDetails[Enter User Details]
    EnterUserDetails --> SaveUser[(Save to Database)]
    EditUser --> UpdateUserDetails[Update User Details]
    UpdateUserDetails --> SaveUser
    AssignRole --> UpdateRole[(Update User Role)]
    
    MembershipManagement --> MembershipActions{Membership Action}
    MembershipActions -->|View All| ViewMemberships[View All Memberships]
    MembershipActions -->|Create Plan| CreatePlan[Create Subscription Plan]
    MembershipActions -->|Process Renewal| ProcessRenewal[Process Renewal]
    MembershipActions -->|Handle Cancellation| HandleCancellation[Handle Cancellation]
    
    ViewMemberships --> FilterMemberships[Filter by Status]
    CreatePlan --> SetPlanDetails[Set Plan Details & Pricing]
    SetPlanDetails --> CreateStripeProduct[Create Stripe Product]
    CreateStripeProduct --> SavePlan[(Save Plan)]
    ProcessRenewal --> UpdateSubscription[(Update Subscription)]
    HandleCancellation --> CancelSubscription[Cancel Subscription]
    CancelSubscription --> UpdateSubscription
    
    FinancialManagement --> FinancialActions{Financial Action}
    FinancialActions -->|View Revenue| ViewRevenue[View Revenue Dashboard]
    FinancialActions -->|Process Payment| ProcessPayment[Process Payment]
    FinancialActions -->|Generate Invoice| GenerateInvoice[Generate Invoice]
    FinancialActions -->|View Reports| ViewFinancialReports[View Financial Reports]
    
    ViewRevenue --> DisplayCharts[Display Revenue Charts]
    ProcessPayment --> EnterPaymentDetails[Enter Payment Details]
    EnterPaymentDetails --> RecordPayment[(Record Payment)]
    GenerateInvoice --> CreateInvoice[Create Invoice Document]
    CreateInvoice --> SaveInvoice[(Save Invoice)]
    
    StaffManagement --> StaffActions{Staff Action}
    StaffActions -->|View Staff| ViewStaff[View All Staff]
    StaffActions -->|Create Account| CreateStaffAccount[Create Staff Account]
    StaffActions -->|Assign Schedule| AssignSchedule[Assign Staff Schedule]
    StaffActions -->|View Performance| ViewPerformance[View Performance Metrics]
    
    ClassManagement --> ClassAdminActions{Class Action}
    ClassAdminActions -->|Create Class| CreateClass[Create New Class]
    ClassAdminActions -->|Assign Trainer| AssignTrainer[Assign Trainer]
    ClassAdminActions -->|Set Capacity| SetCapacity[Set Class Capacity]
    ClassAdminActions -->|View Attendance| ViewClassAttendance[View Class Attendance]
    
    CreateClass --> SetClassDetails[Set Class Details]
    SetClassDetails --> SaveClass[(Save Class)]
    
    FacilityManagement --> FacilityActions{Facility Action}
    FacilityActions -->|Track Equipment| TrackEquipment[Track Equipment Inventory]
    FacilityActions -->|Schedule Maintenance| ScheduleMaintenance[Schedule Maintenance]
    FacilityActions -->|Manage Bookings| ManageBookings[Manage Facility Bookings]
    
    ReportsAnalytics --> ReportOptions{Report Type}
    ReportOptions -->|Membership Stats| MembershipStats[Membership Statistics]
    ReportOptions -->|Revenue Analytics| RevenueAnalytics[Revenue Analytics]
    ReportOptions -->|Attendance Reports| AttendanceReports[Attendance Reports]
    ReportOptions -->|Custom Report| CustomReport[Generate Custom Report]
    
    MembershipStats --> GenerateReport[Generate Report]
    RevenueAnalytics --> GenerateReport
    AttendanceReports --> GenerateReport
    CustomReport --> SelectMetrics[Select Metrics]
    SelectMetrics --> GenerateReport
    GenerateReport --> ExportReport[Export Report PDF/CSV]
    
    SystemSettings --> SettingsOptions{Setting Type}
    SettingsOptions -->|Subscription Plans| ConfigurePlans[Configure Plans]
    SettingsOptions -->|Integrations| ManageIntegrations[Manage Integrations]
    SettingsOptions -->|Notifications| ConfigureNotifications[Configure Notifications]
    SettingsOptions -->|Permissions| ManagePermissions[Manage Permissions]
    
    SaveUser --> End([Action Complete])
    SavePlan --> End
    UpdateSubscription --> End
    RecordPayment --> End
    SaveInvoice --> End
    SaveClass --> End
    ExportReport --> End
```



### 7. Staff User Journey Flow

```mermaid
flowchart TD
    StaffLogin([Staff Logs In]) --> StaffDashboard[Staff Dashboard]
    
    StaffDashboard --> StaffOptions{Select Action}
    
    StaffOptions -->|Check-In| MemberCheckIn[Member Check-In]
    StaffOptions -->|Enrollment| MembershipEnrollment[Membership Enrollment]
    StaffOptions -->|Payments| ProcessPayments[Process Payments]
    StaffOptions -->|Classes| ClassAssistance[Class Assistance]
    StaffOptions -->|Support| MemberSupport[Member Support]
    StaffOptions -->|Reports| DailyReports[Daily Reports]
    
    MemberCheckIn --> ScanMember[Scan/Enter Member ID]
    ScanMember --> VerifyMembership{Verify Membership}
    VerifyMembership -->|Invalid| ShowError[Show Error - Invalid Membership]
    VerifyMembership -->|Expired| ShowExpired[Show Expired Membership]
    VerifyMembership -->|Valid| RecordCheckIn[Record Check-In]
    
    ShowError --> End([Action Complete])
    ShowExpired --> SuggestRenewal[Suggest Renewal]
    SuggestRenewal --> End
    RecordCheckIn --> SaveCheckIn[(Save Check-In)]
    SaveCheckIn --> DisplaySuccess[Display Success Message]
    DisplaySuccess --> End
    
    MembershipEnrollment --> NewMemberInfo[Collect New Member Info]
    NewMemberInfo --> SelectPlan[Select Membership Plan]
    SelectPlan --> ProcessPaymentEnrollment[Process Payment]
    ProcessPaymentEnrollment --> CreateAccount[Create Member Account]
    CreateAccount --> SaveMember[(Save Member)]
    SaveMember --> IssueCard[Issue Membership Card]
    IssueCard --> End
    
    ProcessPayments --> PaymentType{Payment Type}
    PaymentType -->|Membership| MembershipPayment[Membership Payment]
    PaymentType -->|Renewal| RenewalPayment[Renewal Payment]
    PaymentType -->|Upgrade| UpgradePayment[Upgrade Payment]
    
    MembershipPayment --> EnterAmount[Enter Payment Amount]
    RenewalPayment --> EnterAmount
    UpgradePayment --> EnterAmount
    EnterAmount --> ProcessPaymentMethod[Process Payment Method]
    ProcessPaymentMethod --> RecordPaymentStaff[(Record Payment)]
    RecordPaymentStaff --> IssueReceipt[Issue Receipt]
    IssueReceipt --> End
    
    ClassAssistance --> ClassAction{Class Action}
    ClassAction -->|Book Class| BookClassForMember[Book Class for Member]
    ClassAction -->|Cancel Booking| CancelBooking[Cancel Booking]
    ClassAction -->|View Schedule| ViewClassSchedule[View Class Schedule]
    ClassAction -->|Waitlist| ManageWaitlist[Manage Waitlist]
    
    BookClassForMember --> SelectMemberClass[Select Member]
    SelectMemberClass --> SelectClass[Select Class]
    SelectClass --> CheckAvailability{Class Available?}
    CheckAvailability -->|Yes| ConfirmBooking[Confirm Booking]
    CheckAvailability -->|No| AddToWaitlist[Add to Waitlist]
    ConfirmBooking --> SaveBookingStaff[(Save Booking)]
    AddToWaitlist --> SaveWaitlistStaff[(Save to Waitlist)]
    SaveBookingStaff --> End
    SaveWaitlistStaff --> End
    
    MemberSupport --> SupportType{Support Type}
    SupportType -->|General Inquiry| AnswerInquiry[Answer Inquiry]
    SupportType -->|Account Issue| ResolveAccount[Resolve Account Issue]
    SupportType -->|Complaint| HandleComplaint[Handle Complaint]
    SupportType -->|Technical| EscalateTechnical[Escalate to Admin]
    
    AnswerInquiry --> LogInteraction[(Log Interaction)]
    ResolveAccount --> LogInteraction
    HandleComplaint --> LogInteraction
    EscalateTechnical --> LogInteraction
    LogInteraction --> End
    
    DailyReports --> ReportType{Report Type}
    ReportType -->|Attendance| AttendanceReport[Generate Attendance Report]
    ReportType -->|Revenue| RevenueReport[Generate Revenue Report]
    ReportType -->|Shift Summary| ShiftSummary[Generate Shift Summary]
    
    AttendanceReport --> ViewReport[View Report]
    RevenueReport --> ViewReport
    ShiftSummary --> ViewReport
    ViewReport --> End
```



### 8. Workout Planner Flow

```mermaid
flowchart TD
    StartWorkout([User Accesses Workout Planner]) --> CheckRole{User Role?}
    
    CheckRole -->|Member| MemberWorkoutFlow[Member Workout Flow]
    CheckRole -->|Trainer| TrainerWorkoutFlow[Trainer Workout Flow]
    
    MemberWorkoutFlow --> MemberWorkoutOptions{Member Action}
    MemberWorkoutOptions -->|View Plans| ViewAssignedPlans[View Assigned Plans]
    MemberWorkoutOptions -->|Create Custom| CreateCustomWorkout[Create Custom Workout]
    MemberWorkoutOptions -->|Log Session| LogWorkoutSession[Log Workout Session]
    MemberWorkoutOptions -->|View History| ViewWorkoutHistory[View Workout History]
    
    ViewAssignedPlans --> FetchPlans[(Fetch from Database)]
    FetchPlans --> DisplayPlans[Display Workout Plans]
    DisplayPlans --> ViewExercises[View Exercise Details]
    ViewExercises --> StartWorkout[Start Workout]
    StartWorkout --> LogWorkoutSession
    
    CreateCustomWorkout --> BrowseExercises[Browse Exercise Library]
    BrowseExercises --> SelectExercises[Select Exercises]
    SelectExercises --> SetWorkoutDetails[Set Sets, Reps, Weight]
    SetWorkoutDetails --> SetSchedule[Set Workout Schedule]
    SetSchedule --> SaveCustomWorkout[(Save Custom Workout)]
    SaveCustomWorkout --> End([Workout Saved])
    
    LogWorkoutSession --> SelectWorkoutPlan[Select Workout Plan]
    SelectWorkoutPlan --> LogExercise[Log Each Exercise]
    LogExercise --> RecordSets[Record Sets Completed]
    RecordSets --> RecordReps[Record Reps]
    RecordReps --> RecordWeight[Record Weight Used]
    RecordWeight --> RecordRest[Record Rest Time]
    RecordRest --> MoreExercises{More Exercises?}
    MoreExercises -->|Yes| LogExercise
    MoreExercises -->|No| SaveWorkoutLog[(Save Workout Log)]
    SaveWorkoutLog --> UpdateProgress[(Update Progress)]
    UpdateProgress --> End
    
    ViewWorkoutHistory --> FetchHistory[(Fetch Workout History)]
    FetchHistory --> DisplayHistory[Display History with Charts]
    DisplayHistory --> End
    
    TrainerWorkoutFlow --> TrainerWorkoutOptions{Trainer Action}
    TrainerWorkoutOptions -->|Create Plan| CreateWorkoutPlan[Create Workout Plan]
    TrainerWorkoutOptions -->|Assign Plan| AssignWorkoutPlan[Assign to Client]
    TrainerWorkoutOptions -->|View Client Progress| ViewClientProgress[View Client Progress]
    
    CreateWorkoutPlan --> SelectClient[Select Client]
    SelectClient --> ChooseTemplate{Use Template?}
    ChooseTemplate -->|Yes| SelectTemplate[Select Workout Template]
    ChooseTemplate -->|No| BuildFromScratch[Build from Scratch]
    
    SelectTemplate --> CustomizeTemplate[Customize Template]
    CustomizeTemplate --> AddExercises[Add/Remove Exercises]
    BuildFromScratch --> AddExercises
    
    AddExercises --> SetExerciseDetails[Set Exercise Details]
    SetExerciseDetails --> SetProgression[Set Progression Rules]
    SetProgression --> SetDuration[Set Plan Duration]
    SetDuration --> SaveWorkoutPlan[(Save Workout Plan)]
    SaveWorkoutPlan --> AssignWorkoutPlan
    
    AssignWorkoutPlan --> SelectClientAssign[Select Client]
    SelectClientAssign --> ConfirmAssignment[Confirm Assignment]
    ConfirmAssignment --> SaveAssignment[(Save Assignment)]
    SaveAssignment --> NotifyClient[Notify Client]
    NotifyClient --> End
    
    ViewClientProgress --> SelectClientView[Select Client]
    SelectClientView --> FetchClientData[(Fetch Client Data)]
    FetchClientData --> DisplayProgress[Display Progress Charts]
    DisplayProgress --> AnalyzeProgress[Analyze Progress]
    AnalyzeProgress --> AdjustPlan{Adjust Plan?}
    AdjustPlan -->|Yes| CreateWorkoutPlan
    AdjustPlan -->|No| End
```



### 9. System Architecture Overview

```mermaid
flowchart TB
    subgraph Client["Client Layer"]
        WebApp[Next.js Web App]
        MobileApp[Mobile App - Future]
    end
    
    subgraph NextJS["Next.js Application Layer"]
        AppRouter[App Router]
        Middleware[Middleware - Auth & Route Protection]
        APIRoutes[API Routes]
        ServerComponents[Server Components]
    end
    
    subgraph Auth["Authentication Layer"]
        BetterAuth[Better Auth]
        SessionMgmt[Session Management]
        RBAC[Role-Based Access Control]
    end
    
    subgraph Services["External Services"]
        SupabaseDB[(Supabase PostgreSQL)]
        StripeAPI[Stripe API]
        GeminiAPI[Google Gemini API]
        EmailService[Email Service]
    end
    
    subgraph DataFlow["Data Flow"]
        UserActions[User Actions]
        AuthCheck[Authentication Check]
        RoleCheck[Role Verification]
        DataOperations[Data Operations]
        ExternalAPIs[External API Calls]
    end
    
    WebApp --> AppRouter
    MobileApp -.Future.-> AppRouter
    
    AppRouter --> Middleware
    Middleware --> AuthCheck
    AuthCheck --> BetterAuth
    BetterAuth --> SessionMgmt
    SessionMgmt --> RBAC
    RBAC --> RoleCheck
    
    RoleCheck -->|Authorized| APIRoutes
    RoleCheck -->|Unauthorized| AccessDenied[Access Denied]
    
    APIRoutes --> DataOperations
    DataOperations --> SupabaseDB
    
    APIRoutes --> ExternalAPIs
    ExternalAPIs --> StripeAPI
    ExternalAPIs --> GeminiAPI
    ExternalAPIs --> EmailService
    
    SupabaseDB --> ServerComponents
    ServerComponents --> WebApp
    
    StripeAPI --> WebhookHandler[Webhook Handler]
    WebhookHandler --> SupabaseDB
```



## Project Structure

```javascript
gym/
├── app/
│   ├── (auth)/
│   │   ├── login/
│   │   ├── register/
│   │   └── layout.tsx
│   ├── (dashboard)/
│   │   ├── admin/
│   │   ├── member/
│   │   ├── trainer/
│   │   ├── staff/
│   │   └── layout.tsx
│   ├── api/
│   │   ├── auth/
│   │   ├── workouts/
│   │   ├── diet/
│   │   └── stripe/
│   ├── layout.tsx
│   └── page.tsx
├── components/
│   ├── auth/
│   ├── dashboard/
│   ├── workout/
│   ├── diet/
│   └── ui/
├── lib/
│   ├── auth.ts
│   ├── supabase.ts
│   ├── stripe.ts
│   └── gemini.ts
├── types/
│   └── index.ts
└── middleware.ts
```



## Implementation Plan

### Phase 1: Project Setup & Configuration

1. **Initialize Next.js Project**

- Set up Next.js 15 with TypeScript
- Configure Tailwind CSS
- Install shadcn/ui components
- Set up ESLint and Prettier

2. **Environment Configuration**

- Create `.env.local` with:
    - Supabase URL and keys
    - Better Auth secret and configuration
    - Stripe API keys
    - Google Gemini API key
    - Next.js environment variables

3. **Database Schema (Supabase)**

- Users table (extends Better Auth user)
- Roles table (admin, member, trainer, staff)
- User profiles table (extended user information)
- Subscriptions table (Stripe integration)
- Subscription plans table
- Payments table
- Invoices table
- Workouts table (exercise library)
- Workout plans table
- Workout sessions table (logged workouts)
- Workout templates table
- Diet plans table
- Meal plans table
- Nutrition logs table
- Progress tracking table (measurements, weight, photos)
- Goals table
- Classes table
- Class bookings table
- Class schedules table
- Personal training sessions table
- Equipment table
- Facility bookings table
- Lockers table
- Check-ins table
- Messages/communications table
- Notifications table
- Announcements table
- Activity logs table
- Reports table

### Phase 2: Authentication & Authorization

4. **Better Auth Setup**

- Configure Better Auth with Supabase adapter
- Set up email/password authentication
- Implement role-based access control
- Create middleware for route protection
- Add role-based redirects after login

5. **Protected Routes & Middleware**

- Create middleware.ts for route protection
- Implement role-based route guards
- Set up redirect logic based on user roles
- Add session management

### Phase 3: Database & API Layer

6. **Supabase Integration**

- Set up Supabase client
- Create database schema migrations
- Implement Row Level Security (RLS) policies
- Create API routes for data operations

7. **API Routes**

- `/api/auth/*` - Better Auth endpoints
- `/api/workouts/*` - Workout CRUD operations
- `/api/diet/*` - Diet plan generation and management
- `/api/stripe/*` - Subscription webhooks and management

### Phase 4: Subscription Management

8. **Stripe Integration**

- Set up Stripe account and products
- Create subscription plans (monthly, yearly)
- Implement checkout flow
- Set up webhook handlers for subscription events
- Add subscription status checks
- Implement feature gating based on subscription

### Phase 5: Workout Planner

9. **Workout Planner Features**

- Create workout plan builder
- Exercise database/library
- Custom workout creation
- Workout templates
- Progress tracking
- Calendar view for scheduled workouts
- Exercise logging and history

### Phase 6: AI Diet Planner

10. **Google Gemini Integration**

    - Set up Gemini API client
    - Create diet plan generation endpoint
    - Implement prompt engineering for personalized plans
    - Add dietary restrictions and preferences handling
    - Create meal plan display components
    - Add nutrition tracking

11. **Diet Planner UI**

    - Diet plan generation form
    - Meal plan calendar view
    - Nutrition breakdown display
    - Shopping list generation
    - Recipe details and instructions

### Phase 7: Dashboard & UI

12. **Role-Based Dashboards & Features**

#### Admin Role Features

**User Management**

- View all users (members, trainers, staff)
- Create, edit, and delete user accounts
- Assign and modify user roles
- Activate/deactivate accounts
- View user activity logs
- Search and filter users
- Bulk user operations
- Export user data

**Membership Management**

- View all active/inactive memberships
- Create and manage subscription plans
- Process membership renewals
- Handle membership cancellations
- View membership history
- Track membership expiration dates
- Send renewal reminders
- Manage membership upgrades/downgrades

**Financial Management**

- View revenue dashboard (daily, weekly, monthly, yearly)
- Process payments and refunds
- Generate invoices
- View payment history
- Track outstanding payments
- Financial reports and analytics
- Export financial data
- Integration with accounting systems

**Staff Management**

- Manage trainer accounts
- Manage front desk staff accounts
- Assign staff schedules
- View staff performance metrics
- Staff attendance tracking
- Payroll integration (optional)

**Class & Schedule Management**

- Create and manage fitness classes
- Assign trainers to classes
- Set class capacity limits
- Manage class schedules
- View class attendance
- Cancel or reschedule classes
- Waitlist management

**Facility Management**

- Equipment inventory tracking
- Equipment maintenance scheduling
- Facility booking management
- View facility usage statistics
- Manage locker assignments

**Reporting & Analytics**

- Membership statistics dashboard
- Revenue analytics and trends
- Attendance reports
- Class popularity metrics
- Member retention analysis
- Trainer performance reports
- Equipment usage reports
- Custom report builder
- Export reports (PDF, CSV, Excel)

**System Configuration**

- Configure subscription plans and pricing
- Set system-wide settings
- Manage integrations (Stripe, email, etc.)
- Configure notification templates
- Set up automated emails
- Manage system permissions
- View system logs
- Backup and restore data

**Communication**

- Send announcements to all users
- Send targeted messages to specific groups
- View communication history
- Email template management

#### Member Role Features

**Profile Management**

- View and edit personal information
- Update profile picture
- Manage contact details
- View membership details and status
- View subscription plan details
- Download membership card

**Workout Management**

- View assigned workout plans
- Create custom workout plans
- Access exercise library
- Log workout sessions
- Track workout history
- View workout calendar
- Set workout reminders
- Mark workouts as complete
- View workout statistics

**Diet & Nutrition**

- Generate AI-powered diet plans
- View assigned diet plans
- Access meal plan calendar
- View nutrition breakdown (calories, macros, micros)
- Generate shopping lists
- View recipe details
- Track daily food intake
- Log meals and snacks
- View nutrition progress

**Progress Tracking**

- View progress dashboard
- Track body measurements
- Log weight and body fat percentage
- View progress photos
- Track fitness goals
- View progress charts and graphs
- Set and update fitness goals
- View achievement badges

**Class Booking**

- Browse available fitness classes
- Book classes
- View booked classes
- Cancel class bookings
- Join class waitlists
- View class schedule
- Receive class reminders
- View class history

**Personal Training**

- Request personal training sessions
- View trainer profiles
- Book training sessions
- View training session history
- Message assigned trainer
- View training session notes

**Payment Management**

- View subscription details
- Update payment methods
- View billing history
- Download invoices
- Renew membership
- Upgrade/downgrade subscription
- View payment receipts
- Set up auto-renewal

**Communication**

- Receive notifications
- Message trainers
- View announcements
- Contact support
- View message history

**Achievements & Rewards**

- View earned badges
- Track milestones
- View leaderboards (optional)
- View reward points (if applicable)

#### Trainer Role Features

**Client Management**

- View assigned clients list
- View client profiles and details
- View client progress and history
- Search and filter clients
- View client subscription status
- Client activity tracking

**Workout Planning**

- Create personalized workout plans for clients
- Assign workout plans to clients
- Edit and update client workout plans
- Use workout templates
- Access exercise library
- Create custom exercises
- Schedule workouts for clients
- View client workout completion

**Diet Planning**

- Create personalized diet plans for clients
- Assign diet plans to clients
- Edit and update client diet plans
- Use AI to generate diet plans
- View client nutrition tracking
- Provide dietary recommendations

**Session Management**

- Schedule personal training sessions
- View training session calendar
- Manage session availability
- Cancel or reschedule sessions
- View session history
- Log session notes
- Track session attendance

**Progress Monitoring**

- View client progress dashboard
- Track client body measurements
- View client progress photos
- Log client progress notes
- Generate progress reports
- Compare client progress over time
- Set and track client goals

**Communication**

- Message clients
- Send workout reminders
- Send motivational messages
- Receive client messages
- View communication history
- Send progress updates

**Class Management**

- View assigned classes
- Manage class schedules
- View class attendance
- Take class attendance
- View class participant list
- Manage class capacity

**Analytics**

- View client engagement metrics
- Track client retention
- View session statistics
- View class attendance rates
- View personal performance metrics

**Resources**

- Access training resources
- Share resources with clients
- Create training content
- View exercise database

#### Staff Role Features

**Member Check-In/Check-Out**

- Check members in/out at front desk
- View member check-in history
- Verify membership status
- Handle guest passes
- View real-time gym occupancy

**Membership Enrollment**

- Assist with new member registration
- Process membership sign-ups
- Explain membership plans
- Process membership upgrades
- Handle membership inquiries

**Payment Processing**

- Process in-person payments
- Issue payment receipts
- Handle cash and card payments
- Process membership renewals
- View payment history
- Handle refund requests (with approval)

**Class Scheduling Assistance**

- Help members book classes
- View class schedules
- Manage class waitlists
- Assist with class cancellations
- Answer class-related inquiries

**Member Support**

- Answer member questions
- Handle member complaints
- Provide general information
- Assist with account issues
- Direct members to appropriate resources

**Facility Management**

- Monitor facility cleanliness
- Report equipment issues
- Manage locker assignments
- Track facility usage
- Handle facility bookings

**Daily Operations**

- View daily schedule
- View member attendance for the day
- View class schedules
- Monitor gym capacity
- Handle day-to-day inquiries

**Reports**

- View daily attendance reports
- View daily revenue reports
- Generate shift reports
- View member activity logs

13. **UI Components**

    - Responsive navigation with role-based menu
    - Dashboard cards and widgets
    - Data tables for admin views
    - Forms for workout/diet creation
    - Progress charts and visualizations

### Phase 8: Production Readiness

14. **Error Handling & Validation**

    - Form validation with Zod
    - Error boundaries
    - Toast notifications
    - Loading states

15. **Security & Performance**

    - Input sanitization
    - Rate limiting on API routes
    - Image optimization
    - Code splitting
    - SEO optimization

16. **Testing & Deployment**