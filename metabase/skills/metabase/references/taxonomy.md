# Pillo 이벤트 & 유저 택소노미

버전: v2 (202601)

---

## 이벤트 택소노미

BigQuery `events` 테이블의 `event_name` 및 `properties` 키 정의.

`properties` 접근: `JSON_VALUE(properties, '$.key_name')`

### App

| event_name | 설명 | properties |
|------------|------|------------|
| first_app_open | 최초 앱 실행 | - |
| app_open | 앱 실행 | - |
| app_link_open | 유저가 푸시 메시지를 누름 | `route`: string (med, tracker, combined, appointment, refill 등) |

### Onboarding

| event_name | 설명 | properties |
|------------|------|------------|
| ob_welcome_view | 온보딩 웰컴 페이지 노출 | - |
| ob_med_count_view | 약 개수 질문 페이지 노출 | - |
| ob_med_count_tap | 약 개수 선택 버튼 탭 | `count`: integer (1,2,3) |
| ob_data_option_tap | 온보딩에서 import/restore 옵션 선택 | `is_medisafe`: boolean |
| ob_med_freq_view | 주기 설정 페이지 노출 | - |
| ob_med_freq_confirm_tap | 주기 설정 다음 버튼 탭 | `schedule`: string (morning, noon, evening, night, others) |
| ob_reminder_type_option_view | 리마인더 스타일 선택 노출 | - |
| ob_reminder_type_option_tap | 리마인더 스타일 선택 완료 | `option`: string (full_screen, small) |
| ob_reminder_style_option_view | 리마인더 테마 선택 노출 | - |
| ob_reminder_style_option_tap | 리마인더 테마 선택 완료 | `option`: string (big, small, basic) |
| ob_reminder_loading_view | 리마인더 설정중 로딩 노출 | - |
| ob_dooa_perm_view | DOOA 권한 요청 노출 | - |
| ob_dooa_perm_2nd_view | DOOA 권한 재요청 페이지 | - |
| ob_dooa_perm_2nd_go_setting_tap | DOOA 권한 재요청 - 설정가기 탭 | - |
| ob_dooa_perm_2nd_ignore_tap | DOOA 권한 재요청 - 필요없어요 탭 | - |

### Today (투데이 탭)

| event_name | 설명 | properties |
|------------|------|------------|
| today_tab_view | 투데이탭 노출 | - |
| today_top_bar_settings_tap | 투데이탭 설정 버튼 탭 | - |
| today_top_bar_log_tap | 투데이탭 로그 버튼 탭 | - |
| today_top_bar_profile_tap | 투데이탭 프로필 버튼 탭 | - |
| today_fab_tap | 투데이탭 FAB 버튼 탭 | - |
| today_fab_option_tap | 투데이탭 FAB 옵션 선택 | `option`: string (med, tracker, appointment) |
| today_box_time_tap | 알림 박스 시간 탭 | - |
| today_box_volume_tap | 알림 박스 볼륨 버튼 탭 | - |
| today_box_name_tap | 알림 박스 아이템 이름 탭 | - |
| today_edit_bs_open | 약/트래커 이름 탭 후 바텀시트 열림 | `type`: string (med, tracker), `log_status`: string (unlogged, logged) |
| today_edit_bs_med_name_tap | 바텀시트에서 이름 수정 버튼 탭 | - |
| today_edit_bs_schedule_tap | 바텀시트에서 스케줄 버튼 탭 | - |
| today_edit_bs_dose_tap | 바텀시트에서 dose 버튼 탭 | - |
| today_edit_bs_refill_tap | 바텀시트에서 refill 버튼 탭 | - |
| today_edit_bs_logging_tap | 바텀시트에서 로그하기 탭 (take/track/skip 포함) | `type`: string (med, tracker) |
| today_edit_bs_edit_logging_tap | 바텀시트에서 로그 수정 탭 | `type`: string (med, tracker) |
| today_edit_bs_change_time_once_tap | 바텀시트에서 스케줄 1회 변경 탭 | - |
| today_prn_extra_dose_tap | PRN 추가 복용 탭 (global) | - |
| today_prn_extra_dose_done_tap | PRN 복용 완료 탭 | - |
| today_prn_box_take_tap | PRN 박스에서 log as taken 탭 | - |
| today_prn_box_take_done | PRN 박스 log as taken 완료 | - |
| today_prn_extra_meas_tap | PRN 추가 측정 탭 (global) | - |
| today_prn_extra_meas_on_box_tap | PRN 박스에서 측정 탭 | - |

### Add Med (약 추가)

| event_name | 설명 | properties |
|------------|------|------------|
| add_med_name_view | 약 이름 입력 페이지 노출 | - |
| add_med_name_next_tap | 약 이름 입력 완료 버튼 탭 | `med_name`: string |
| add_med_form_view | 약 폼 선택 페이지 노출 | - |
| add_med_optional_detail_view | 약 옵셔널 디테일 페이지 노출 | `strength`: boolean |
| add_med_optional_detail_done | 옵셔널 디테일 페이지 완료 | `purpose`: string |

### Edit Med (약 수정)

| event_name | 설명 | properties |
|------------|------|------------|
| edit_med_view | 약 수정 페이지 노출 | - |
| edit_med_suspend_done | suspend 옵션 선택 완료 | `type`: string (set_days, until_resume) |
| edit_med_delete_done | 약 삭제 완료 | `type`: string (reminder, reminder_and_log) |
| edit_med_info_tap | edit info 버튼 탭 | - |
| edit_med_history_tap | history 버튼 탭 | - |
| edit_med_schedule_tap | schedule 모듈 수정 탭 | - |
| edit_med_dose_tap | dose 모듈 수정 탭 | - |
| edit_med_inventory_tap | inventory 모듈 수정 탭 | - |

### Tracker

| event_name | 설명 | properties |
|------------|------|------------|
| add_tracker_option_view | 트래커 종류 선택 노출 | - |
| add_tracker_option_select | 트래커 종류 선택 | - |
| add_tracker_done | 트래커 추가 완료 | `type`: string (water, mood, sleep, weight, blood_pressure, heart_rate, glucose, hba1c, spo2, body_temperature) |
| edit_tracker_view | 트래커 수정 페이지 노출 | - |
| edit_tracker_delete_done | 트래커 삭제 완료 | - |
| edit_tracker_history_tap | 트래커 history 버튼 탭 | - |

### Alarm

| event_name | 설명 | properties |
|------------|------|------------|
| first_alarm_start_view | 앱 설치 후 첫 알람 시작 화면 노출 | - |
| second_alarm_start_view | 앱 설치 후 두번째 알람 시작화면 노출 | - |
| alarm_start_view | 알람 시작 화면 노출 | `count`: integer, `has_med`: boolean, `has_tracker`: boolean, `med_count`: integer, `tracker_count`: integer |
| alarm_finish_view | 알람 완료 화면 노출 (taken/skip/mixed) | `count`: integer, `has_med`: boolean, `has_tracker`: boolean, `med_count`: integer, `tracker_count`: integer, `reach_adherence_goal`: boolean, `record`: string (taken, skipped, mixed) |
| alarm_finish_close | 알람 완료 화면 닫힘 | `touch_close`: boolean |
| alarm_donation_view | 복약순응도 및 Donation 뷰 노출 | - |
| alarm_donation_close | Donation 뷰 닫음 | `donation_done`: boolean |
| reminder_alarm_fired | 알람이 유저에게 실제 노출됨 | `reminder_ids`: List, `tracker_ids`: List, `scheduled_at`: Long(nullable), `fired_at`: Long, `alarm_type`: string (full, light, dedicated) |

### Snooze

| event_name | 설명 | properties |
|------------|------|------------|
| alarm_snoozed_view | 스누즈 화면 노출 | - |
| alarm_snoozed_undo_tap | 스누즈 취소 탭 | - |
| alarm_snoozed_close | 스누즈 화면 닫힘 | `snoozed_min`: integer |

### Feedback

| event_name | 설명 | properties |
|------------|------|------------|
| feedback_modal_view | 피드백 모달 노출 (앱 만족도) | - |
| feedback_modal_answer_tap | 피드백 모달 응답 탭 | `option`: string (yes, no, later) |
| feedback_rate_view | 평가 모달 노출 | - |
| feedback_rate_answer_tap | 평가 모달 응답 탭 | `option`: string (yes, no, later) |

### Settings

| event_name | 설명 |
|------------|------|
| settings_view | 설정 페이지 노출 |
| settings_volume_tap | volume 메뉴 탭 |
| settings_ringtone_tap | ringtone 메뉴 탭 |
| settings_appearance_tap | appearance 메뉴 탭 |
| settings_persistence_tap | persistence 메뉴 탭 |
| settings_dismiss_flow_tap | dismiss_flow 메뉴 탭 |
| settings_smart_snooze_tap | smart_snooze 메뉴 탭 |
| settings_preferences_tap | preferences 메뉴 탭 |
| settings_essential_settings_tap | essential_settings 메뉴 탭 |
| settings_widgets_tap | widgets 메뉴 탭 |
| settings_share_report_tap | share_report 메뉴 탭 |
| settings_backup_tap | backup/restore 메뉴 탭 |
| settings_remove_ads_tap | remove_ads 메뉴 탭 |
| settings_send_feedback_tap | send_feedback 메뉴 탭 |
| settings_share_tap | share 메뉴 탭 |
| settings_rate_tap | rate 메뉴 탭 |

### Share

| event_name | 설명 | properties |
|------------|------|------------|
| share_report_view | 리포트 공유 페이지 노출 | - |
| share_report_submit | 리포트 공유 완료 | `type`: string (med_list, med_records, meas_records, notes, allergies) |

### Subscription

| event_name | 설명 | properties |
|------------|------|------------|
| subscription_paywall_view | paywall 노출 | - |
| subscription_subscribe_done | 구독 완료 | `type`: string (monthly_adfree, yearly_adfree, monthly_premium, yearly_premium) |

### Log

| event_name | 설명 | properties |
|------------|------|------------|
| scheduled_med_log_manually_done | 투데이탭 매뉴얼 약 로깅 완료 | `med_count`: integer, `log_type`: string (taken, skipped), `timing`: string (before_alarm, after_alarm) |
| scheduled_tracker_log_manually_done | 투데이탭 매뉴얼 트래커 로깅 완료 | `type`: string (tracker 종류), `log_type`: string (done, skipped), `timing`: string (before_alarm, after_alarm) |
| reminder_event_logged | Today 탭에서 수동 로깅 (taken/skipped) | `reminder_ids`: List, `tracker_ids`: List, `scheduled_at`: Long(nullable), `logged_at`: Long, `action`: string (taken, skipped), `route`: string |

---

## 유저 프로퍼티 택소노미

BigQuery `user_profiles` 테이블의 `properties` JSON 내 키 정의.

`properties` 접근: `JSON_VALUE(properties, '$.key_name')`

### Profile (프로필)

| key | 설명 | type | 예시 |
|-----|------|------|------|
| prof_first_open_date | 최초 앱 실행 일자 (UTC) | date | 2022-01-01 |
| prof_birth_year | 출생 연도 | string | 1962 |
| prof_birth_month | 출생 월 | integer | 1-12 |
| prof_gender | 성별 | string | male, female, other, none |
| prof_height_set | 키 등록 여부 | boolean | true, false |
| prof_weight_set | 몸무게 등록 여부 | boolean | true, false |
| prof_name | 이름 등록 여부 | boolean | true, false |
| prof_email | 이메일 등록 여부 | boolean | true, false |
| prof_conditions | 등록된 질환명 | string | - |
| prof_allergies | 등록된 알레르기 | string | - |
| prof_count_doctors | 등록한 의사 수 | integer | 0-99 |
| prof_count_pharmacies | 등록한 약국 수 | integer | 0-99 |
| prof_count_dependents | 등록한 디펜던트 수 | integer | 0-99 |
| prof_count_dependent_meds | 디펜던트 총 약 개수 | integer | 0-99 |
| prof_count_caregivers | 케어기버 수 | integer | 0-9 |
| prof_count_appointments | 등록한 약속 개수 | integer | 0-999 |
| prof_count_notes | 입력한 노트 개수 | integer | 0-99999 |
| prof_medisafe_uploaded | 메디세이프 리포트 업로드 여부 | boolean | true, false |
| prof_prescription_added | 처방전 추가 여부 | boolean | true, false |

### Account (계정)

| key | 설명 | type | 예시 |
|-----|------|------|------|
| acct_signed_in | 로그인 여부 | boolean | true, false |
| acct_auto_backup_on | 자동 백업 설정 여부 | boolean | true, false |
| acct_pinned_widget | 고정된 위젯 목록 | string | TODAY, CABINET |
| acct_setting_dark_theme | 다크 테마 활성화 | boolean | true, false |
| acct_setting_app_lock | 앱 잠금 활성화 | boolean | true, false |
| acct_setting_hide_med_name | 약 이름 숨김 | boolean | true, false |
| acct_setting_dynamic_dosage | 동적 복용량 설정 여부 | boolean | true, false |
| acct_ad_eligible | 광고 노출 대상 여부 | boolean | true, false |
| acct_subscription_status | 구독 상태 | string | none, premium_yearly, premium_monthly, ad_free_yearly, ad_free_monthly |
| acct_donation_status | 기부 상태 | string | auto, manual, disabled |
| acct_donation_charity | 선택한 기부 자선단체 | string | DirectRelief |

### Stats (통계)

| key | 설명 | type |
|-----|------|------|
| stats_using_days | 앱 사용 일수 | integer |
| stats_last_active_days_elapsed | 마지막 활성일 이후 비활성 지속일 수 | integer |
| stats_weekly_active_days | 최근 7일간 활성일 수 | integer (0-7) |
| stats_adherence_7d | 최근 7일간 복약순응도 | integer (0-100) |
| stats_adherence_14d | 최근 14일간 복약순응도 | integer (0-100) |
| stats_adherence_30d | 최근 30일간 복약순응도 | integer (0-100) |
| stats_streak_max | 최대 연속 복약 일수 | integer |
| stats_count_donations | 도네이션 횟수 | integer |
| stats_count_interstitial_ad_impressions | 전면 광고 노출 횟수 | integer |
| stats_ob_schedules | 온보딩 선택 스케줄 수 | integer (0-4) |
| stats_ob_med_edited | 온보딩 약 수정 경험 여부 | boolean |

### System (시스템)

| key | 설명 | type |
|-----|------|------|
| sys_build_type | 빌드 종류 | string (release, debug, test) |
| sys_perm_display_over_other_apps | 다른 앱 위에 표시 권한 | boolean |
| sys_perm_notification | 푸시 알림 허용 여부 | boolean |
| sys_perm_battery_unrestricted | 베터리 최적화 무시 여부 | boolean |

### Medication (약)

| key | 설명 | type |
|-----|------|------|
| med_count_total | 복약중인 약의 개수 | integer |
| med_count_scheduled | 스케줄 설정한 약의 개수 | integer |
| med_count_prn | as-needed로 설정한 약의 개수 | integer |
| med_count_suspended | suspend한 약의 개수 | integer |
| med_count_name_set | 실제 이름이 있는 약 개수 | integer |
| med_count_dose_set | 복용량 입력한 약의 개수 | integer |
| med_count_strength_set | strength 입력한 약의 개수 | integer |
| med_count_purpose_set | purpose 입력한 약의 개수 | integer |
| med_count_stock_set | stock 입력한 약의 개수 | integer |
| med_count_added_after_onboarding | 온보딩 이후 추가한 약 개수 | integer |
| med_count_daily | 주기가 매일인 약 개수 | integer |
| med_count_non_daily | 주기가 매일이 아닌 약의 개수 | integer |
| med_name_changed_during_ob | 온보딩에서 약 이름 수정 여부 | boolean |

### Reminder (리마인더)

| key | 설명 | type |
|-----|------|------|
| reminder_count_dependent | 디펜던트 리마인더 수 | integer |
| reminder_volume_daysofsilence_enabled | 무음 모드 활성화 | boolean |
| reminder_volume_power_btn_to_silence | 파워 버튼 음소거 설정 | boolean |
| reminder_volume_device_speaker_used | 디바이스 스피커 사용 설정 | boolean |
| reminder_volume_follow_device_volume | 디바이스 볼륨 따라가기 설정 | boolean |
| reminder_ringtone_fullscreen_type | 리마인더 음원 설정 타입 | string (voice, music, one_time, own) |
| reminder_ringtone_fullscreen_option | 리마인더 음원 이름 | string |
| reminder_appearance_fullscreen_enabled | 전체화면 알람 가능 여부 | boolean |
| reminder_appearance_theme | 리마인더 화면 테마 | string (big_face, small_face, basic) |
| reminder_persistence_force_log_enabled | force_log 설정 여부 | boolean |
| reminder_persistence_snoozeonexit_enabled | snooze_on_exit 설정 여부 | boolean |
| reminder_persistence_bypass_dnd_enabled | bypass dnd 설정 여부 | boolean |
| reminder_persistence_battery_save_enabled | 베터리 세이브 모드 설정 여부 | boolean |
| reminder_persistence_auto_dismiss_enabled | 알람 자동 해제 설정 여부 | boolean |
| reminder_flow_timebox_enabled | 알람 타이머 활성화 여부 | boolean |
| reminder_flow_dose_note | 복용 노트 활성화 여부 | boolean |
| reminder_flow_skip_note | 스킵 노트 활성화 여부 | boolean |
| reminder_flow_adjust_dose_while_log | 로깅중 복용량 수정 활성화 | boolean |
| reminder_flow_donation | 로깅 이후 도네이션 화면 노출 여부 | boolean |
| reminder_snooze_driving_safety_enabled | 운전 안전 모드 활성화 | boolean |
| reminder_snooze_phone_in_use_enabled | 다른 앱 사용 중 스누즈 활성화 | boolean |
| reminder_snooze_on_call_enabled | 통화중 자동 스누즈 활성화 | boolean |
| reminder_snooze_place_set | 장소 기반 스누즈 설정 | set |
| reminder_snooze_dose_spacing_enabled | 복용 간격 유지 설정 | boolean |
| active_reminder_schedules | 현재 활성화된 전체 리마인더 스케줄 | string (JSON array) |

### Health (건강)

| key | 설명 | type |
|-----|------|------|
| health_count_trackers | 등록한 트래커 개수 | integer |
| health_count_tracker_scheduled | 리마인더 설정된 트래커 개수 | integer |
| health_count_tracker_asneeded | as-needed 트래커 개수 | integer |
| health_tracker_enabled | 활성화된 트래커 종류 | set (bp, weight, glucose, hba1c, heart_rate, spo2, water, temp, mood, sleep) |
