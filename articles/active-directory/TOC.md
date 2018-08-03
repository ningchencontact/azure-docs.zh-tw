# [Azure Active Directory 文件](index.md)

# 概觀
## [什麼是 Azure Active Directory？](fundamentals/active-directory-whatis.md)
## [關於 Azure 身分識別管理](fundamentals/identity-fundamentals.md)
## [了解 Azure 身分識別解決方案](fundamentals/understand-azure-identity-solutions.md)
## [選擇混合式身分識別解決方案](choose-hybrid-identity-solution.md)
## [關聯 Azure 訂用帳戶](fundamentals/active-directory-how-subscriptions-associated-directory.md)
## [常駐和資料考量](fundamentals/active-directory-data-storage-eu.md)
## [常見問題集](fundamentals/active-directory-faq.md)
## [新功能](fundamentals/whats-new.md)


# 開始使用
## [開始使用 Azure AD](fundamentals/get-started-azure-ad.md)
## [註冊 Azure AD Premium](fundamentals/active-directory-get-started-premium.md)
## [新增自訂網域名稱](fundamentals/add-custom-domain.md)
## [設定公司商標](fundamentals/customize-branding.md)
## [在 Azure AD 中新增使用者](fundamentals/add-users-azure-active-directory.md)
## [將授權指派給使用者](fundamentals/license-users-groups.md)
## [啟用自助式密碼重設](authentication/quickstart-sspr.md)
## [在 Azure AD 中新增貴組織的隱私權資訊](active-directory-properties-area.md)


# 作法
## 規劃和設計
### [了解 Azure AD 架構](fundamentals/active-directory-architecture.md)
### [Azure Active Directory 中的宣告對應](active-directory-claims-mapping.md)
### [部署混合式身分識別解決方案](active-directory-hybrid-identity-design-considerations-overview.md)
#### 判斷需求
##### [身分識別](active-directory-hybrid-identity-design-considerations-business-needs.md)
##### [目錄同步](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)
##### [多因素驗證](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)
##### [身分識別生命週期策略](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)
#### [資料安全性的規劃](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md)
##### [資料保護](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)
##### [內容管理](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)
##### [存取控制](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)
##### [事件回應](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)
#### 規劃身分識別生命週期
##### [工作](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)
##### [採用策略](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md)
#### [後續步驟](active-directory-hybrid-identity-design-considerations-nextsteps.md)
#### [工具比較](active-directory-hybrid-identity-design-considerations-tools-comparison.md)

## 管理使用者
### [在 Azure AD 中新增使用者](fundamentals/add-users-azure-active-directory.md)
### [管理使用者設定檔](fundamentals/active-directory-users-profile-azure-portal.md)
### [重設使用者密碼](fundamentals/active-directory-users-reset-password-azure-portal.md)
### [共用帳戶](active-directory-sharing-accounts.md)
### [將使用者指派為管理員角色](fundamentals/active-directory-users-assign-role-azure-portal.md)
### [還原已刪除的使用者](fundamentals/active-directory-users-restore.md)
### [從另一個目錄 (B2B) 中新增來賓使用者](b2b/what-is-b2b.md)
#### [管理員新增 B2B 使用者](b2b/add-users-administrator.md)
#### [資訊背景工作新增 B2B 使用者](b2b/add-users-information-worker.md)
#### [API 與自訂](b2b/customize-invitation-api.md)
#### [程式碼和 Azure PowerShell 範例](b2b/code-samples.md)
#### [自助入口網站註冊範例](b2b/self-service-portal.md)
#### [邀請電子郵件](b2b/invitation-email-elements.md)
#### [兌換邀請](b2b/redemption-experience.md)
#### [在沒有邀請的情況下新增 B2B 使用者](b2b/add-user-without-invite.md)
#### [允許或封鎖邀請](b2b/allow-deny-list.md)
#### [B2B 的條件式存取](b2b/conditional-access.md)
#### [B2B 共用原則](b2b/delegate-invitations.md)
#### [將 B2B 使用者新增至角色](b2b/add-guest-to-role.md)
#### [動態群組和 B2B 使用者](b2b/use-dynamic-groups.md)
#### [離開組織](b2b/leave-the-organization.md)
#### [稽核與報告](b2b/auditing-and-reporting.md)
#### [適用於混合式組織的 B2B](b2b/hybrid-organizations.md)
##### [授與 B2B 使用者本機應用程式的存取權限](b2b/hybrid-cloud-to-on-premises.md)
##### [授與本機使用者雲端應用程式的存取權限](b2b/hybrid-on-premises-to-cloud.md)
#### [B2B 和 Office 365 外部共用](b2b/o365-external-user.md)
#### [B2B 授權](b2b/licensing-guidance.md)
#### [目前限制](b2b/current-limitations.md)
#### [常見問題集](b2b/faq.md)
#### [疑難排解 B2B](b2b/troubleshoot.md)
#### [了解 B2B 使用者](b2b/user-properties.md)
#### [B2B 使用者權杖](b2b/user-token.md)
#### [適用於 Azure AD 整合應用程式的 B2B](b2b/configure-saas-apps.md)
#### [B2B 使用者宣告對應](b2b/claims-mapping.md)
#### [比較 B2B 共同作業與 B2C](b2b/compare-with-b2c.md)
#### [取得 B2B 支援](b2b/get-support.md)

## [管理群組和成員](fundamentals/active-directory-manage-groups.md)
### 管理群組
#### [Azure 入口網站](fundamentals/active-directory-groups-create-azure-portal.md)
#### [Azure AD PowerShell for Graph (v2)](users-groups-roles/groups-settings-v2-cmdlets.md)
#### [Azure AD PowerShell MSOnline](users-groups-roles/groups-settings-cmdlets.md)
### [管理群組成員](fundamentals/active-directory-groups-members-azure-portal.md)
### [管理群組擁有者](fundamentals/active-directory-accessmanagement-managing-group-owners.md)
### [管理群組成員資格](fundamentals/active-directory-groups-membership-azure-portal.md)
### [使用群組指派授權](fundamentals/active-directory-licensing-whatis-azure-portal.md)
#### [將授權指派給群組](users-groups-roles/licensing-groups-assign.md)
#### [識別並解決群組的授權問題](users-groups-roles/licensing-groups-resolve-problems.md)
#### [將個別授權使用者移轉至群組型的授權](users-groups-roles/licensing-groups-migrate-users.md)
#### [在產品授權間移轉使用者](users-groups-roles/licensing-groups-change-licenses.md)
#### [群組型授權的其他案例](users-groups-roles/licensing-group-advanced.md)
#### [群組型授權的 Azure PowerShell 範例](users-groups-roles/licensing-ps-examples.md)
#### [Azure AD 中的產品與服務方案的參考](users-groups-roles/licensing-service-plan-reference.md)
### [設定 Office 365 群組到期時間](users-groups-roles/groups-lifecycle.md)
### [為群組強制執行命名原則](users-groups-roles/groups-naming-policy.md)
### [檢視全部群組](fundamentals/active-directory-groups-view-azure-portal.md)
### [新增 SaaS 應用程式的群組存取權](users-groups-roles/groups-saasapps.md)
### [還原已刪除的 Office 365 群組](fundamentals/active-directory-groups-restore-azure-portal.md)
### [管理群組設定](fundamentals/active-directory-groups-settings-azure-portal.md)
### [設定自助式群組](users-groups-roles/groups-self-service-management.md)
### 動態群組
#### [建立動態群組](users-groups-roles/groups-create-rule.md)
#### [規則語法和屬性](users-groups-roles/groups-dynamic-membership.md)
#### [變更群組成員資格](users-groups-roles/groups-change-type.md)
#### [疑難排解](users-groups-roles/groups-troubleshooting.md)

## [管理報告](active-directory-reporting-azure-portal.md)
### [登入活動](active-directory-reporting-activity-sign-ins.md)
### [稽核活動](active-directory-reporting-activity-audit-logs.md)
### [有風險的使用者](active-directory-reporting-security-user-at-risk.md)
### [有風險的登入](active-directory-reporting-security-risky-sign-ins.md)
### [風險事件](active-directory-reporting-risk-events.md)
### [用 Azure 監視器 監視記錄](reporting-azure-monitor-diagnostics-overview.md)
### [常見問題集](active-directory-reporting-faq.md)

### 工作
#### [設定具名位置](active-directory-named-locations.md)
#### [尋找活動報告](active-directory-reporting-migration.md)
#### [使用 Azure AD Power BI 內容套件](active-directory-reporting-power-bi-content-pack-how-to.md)
#### [修復標幟為有風險的使用者](active-directory-report-security-user-at-risk-remediation.md)
#### [將活動記錄路由至 Azure 事件中樞](reporting-azure-monitor-diagnostics-azure-event-hub.md)
#### [將活動記錄封存至 Azure 儲存體帳戶](reporting-azure-monitor-diagnostics-azure-storage-account.md)
#### [使用 Azure 監視器整合活動記錄與 Splunk](reporting-azure-monitor-diagnostics-splunk-integration.md)

### 參考
#### [保留](active-directory-reporting-retention.md)
#### [延遲](active-directory-reporting-latencies-azure-portal.md)
#### [稽核活動參考](active-directory-reporting-activity-audit-reference.md)
#### [登入活動的錯誤代碼](active-directory-reporting-activity-sign-ins-errors.md)
#### [解譯 Azure 監視器中的稽核記錄結構描述](reporting-azure-monitor-diagnostics-audit-log-schema.md)
#### [解譯 Azure 監視器中的登入記錄結構描述](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)

### 疑難排解
#### [缺少稽核資料](active-directory-reporting-troubleshoot-missing-audit-data.md)
#### [下載中缺少的資料](active-directory-reporting-troubleshoot-missing-data-download.md)
#### [Azure AD 活動記錄內容套件錯誤](active-directory-reporting-troubleshoot-content-pack.md)
#### [Azure AD 報告 API 中的錯誤](active-directory-reporting-troubleshoot-graph-api.md)

### [程式設計存取](active-directory-reporting-api-getting-started-azure-portal.md)
#### [先決條件](active-directory-reporting-api-prerequisites-azure-portal.md)
#### [稽核範例](active-directory-reporting-api-audit-samples.md)
#### [登入範例](active-directory-reporting-api-sign-in-activity-samples.md)
#### [使用憑證](active-directory-reporting-api-with-certificates.md)

## [管理密碼](authentication/concept-sspr-howitworks.md)
### 使用者文件
#### [重設或變更您的密碼](user-help/active-directory-passwords-update-your-own-password.md)
#### [註冊自助式密碼重設](user-help/active-directory-passwords-reset-register.md)


## 管理裝置
### [概觀](devices/overview.md)
### [使用 Azure 入口網站](devices/device-management-azure-portal.md)
### [規劃 Azure AD Join](devices/azureadjoin-plan.md)
### [常見問題集](devices/faq.md)
### 工作
#### [如何規劃混合式 Azure Active Directory Join 實作](devices/hybrid-azuread-join-plan.md)
#### [設定適用於受控網域的混合式 Azure Active Directory Join](devices/hybrid-azuread-join-managed-domains.md)
#### [設定適用於同盟網域的混合式 Azure Active Directory Join](devices/hybrid-azuread-join-federated-domains.md)
#### [如何控制裝置的混合式 Azure AD Join](devices/hybrid-azuread-join-control.md)
#### [設定 10 部已註冊 Azure AD 的 Windows 10 裝置](user-help/device-management-azuread-registered-devices-windows10-setup.md)
#### [設定 Azure AD 已加入裝置](user-help/device-management-azuread-joined-devices-setup.md)
#### [設定混合式 Azure AD 已加入裝置](devices/hybrid-azuread-join-manual-steps.md)
#### [部署內部部署](active-directory-device-registration-on-premises-setup.md)
#### [在第一次執行 Windows 10 時加入 Azure AD 的體驗](devices/azuread-joined-devices-frx.md)
### 疑難排解
#### [混合式 Azure AD 已加入 Windows 10 和 Windows Server 2016 裝置](devices/troubleshoot-hybrid-join-windows-current.md)
#### [混合式 Azure AD 已加入舊版 Windows 裝置](devices/troubleshoot-hybrid-join-windows-legacy.md)

## 管理應用程式
### [概觀](manage-apps/what-is-application-management.md)
### [開始使用](manage-apps/plan-an-application-integration.md)
### [SaaS 應用程式整合教學課程](saas-apps/tutorial-list.md)

### [SaaS 應用程式使用者佈建和解除佈建](active-directory-saas-app-provisioning.md) 
#### [應用程式整合教學課程](saas-apps/tutorial-list.md) 
#### [啟用 SCIM 應用程式的自動化佈建](manage-apps/use-scim-to-provision-users-and-groups.md) 
#### [自訂屬性對應](active-directory-saas-customizing-attribute-mappings.md) 
#### [撰寫屬性對應的運算式](active-directory-saas-writing-expressions-for-attribute-mappings.md) 
#### [使用範圍篩選器](active-directory-saas-scoping-filters.md) 
#### [關於使用者自動佈建的報告](active-directory-saas-provisioning-reporting.md) 
#### [針對使用者佈建進行疑難排解](active-directory-application-provisioning-content-map.md) 

### [透過 App Proxy 遠端存取應用程式](manage-apps/application-proxy.md)
#### 開始使用
##### [啟用應用程式 Proxy](manage-apps/application-proxy-enable.md)
##### [發佈應用程式](manage-apps/application-proxy-publish-azure-portal.md)
##### [自訂網域](manage-apps/application-proxy-configure-custom-domain.md)
#### [單一登入](manage-apps/application-proxy-single-sign-on.md)
##### [搭配 KCD 的 SSO](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
##### [搭配標頭的 SSO](manage-apps/application-proxy-configure-single-sign-on-with-ping-access.md)
##### [搭配密碼保存庫的 SSO](manage-apps/application-proxy-configure-single-sign-on-password-vaulting.md)
#### 概念
##### [連接器](manage-apps/application-proxy-connectors.md)
##### [安全性](manage-apps/application-proxy-security.md)
##### [網路](manage-apps/application-proxy-network-topology.md)


##### [從 TMG 或 UAG 升級](manage-apps/application-proxy-migration.md)

#### 進階組態
##### [在不同網路上發佈](manage-apps/application-proxy-connector-groups.md)
##### [Proxy 伺服器](manage-apps/application-proxy-configure-connectors-with-proxy-servers.md)
##### [宣告感知應用程式](manage-apps/application-proxy-configure-for-claims-aware-applications.md)
##### [原生用戶端應用程式](manage-apps/application-proxy-configure-native-client-application.md)
##### [無訊息安裝](manage-apps/application-proxy-register-connector-powershell.md)
##### [自訂首頁](manage-apps/application-proxy-configure-custom-home-page.md)
##### [翻譯內嵌連結](manage-apps/application-proxy-configure-hard-coded-link-translation.md)
##### [萬用字元](active-directory-application-proxy-wildcard.md)
##### [移除個人資料](manage-apps/application-proxy-remove-personal-data.md)


#### 發佈逐步解說
##### [遠端桌面](manage-apps/application-proxy-integrate-with-remote-desktop-services.md)
##### [SharePoint](manage-apps/application-proxy-integrate-with-sharepoint-server.md)
##### [Microsoft Teams](manage-apps/application-proxy-integrate-with-teams.md)
##### [Tableau](manage-apps/application-proxy-integrate-with-tableau.md)
##### [Qlik](active-directory-application-proxy-qlik.md)
#### [PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management) 

#### [疑難排解](manage-apps/application-proxy-troubleshoot.md)

### 管理企業應用程式
#### [新增應用程式](manage-apps/add-application-portal.md)
#### [檢視租用戶應用程式](manage-apps/view-applications-portal.md)
#### [指派使用者](manage-apps/assign-user-or-group-access-portal.md)
#### [自訂商標](manage-apps/change-name-or-logo-portal.md)
#### [停用使用者登入](manage-apps/disable-user-sign-in-portal.md)
#### [移除使用者](manage-apps/remove-user-or-group-access-portal.md)

#### [管理使用者帳戶佈建](manage-apps/configure-automatic-user-provisioning-portal.md)
#### [管理企業應用程式的單一登入](manage-apps/configure-single-sign-on-portal.md)
#### [適用於 SAML 應用程式的進階憑證](manage-apps/certificate-signing-options.md)
#### [將應用程式排除在使用者體驗之外](manage-apps/hide-application-from-user-portal.md)
### [使用 HRD 原則設定登入時自動加速](manage-apps/configure-authentication-for-federated-users-portal.md)
### [將 AD FS 應用程式移轉至 Azure AD](manage-apps/migrate-adfs-apps-to-azure.md) 
### [管理應用程式的存取權](manage-apps/what-is-access-management.md)
#### [SSO 存取](manage-apps/what-is-single-sign-on.md)
#### [SSO 憑證](manage-apps/manage-certificates-for-federated-single-sign-on.md)
#### [租用戶限制](manage-apps/tenant-restrictions.md)
#### [使用 SCIM 佈建使用者](manage-apps/use-scim-to-provision-users-and-groups.md)


### 疑難排解



#### 存取面板
##### [未顯示的應用程式](application-access-panel-unexpected-application-not-appearing.md)
##### [出現未預期的應用程式](application-access-panel-unexpected-application-appears.md)
##### [無法登入](application-access-panel-web-sign-in-problem.md)
##### [安裝瀏覽器擴充功能時發生錯誤](application-access-panel-extension-problem-installing.md)
##### [如何使用自助應用程式存取](application-access-panel-self-service-applications-how-to.md)
##### [使用自助應用程式存取時發生錯誤](application-access-panel-self-service-applications-problem.md)

#### 新增應用程式
##### [選擇應用程式類型](application-config-add-app-problem-how-to-choose-application-type.md)
##### [常見問題 - 資源庫應用程式](application-config-add-app-problem-problem-adding-gallery-app.md)
##### [常見問題 - 非資源庫應用程式](application-config-add-app-problem-problem-adding-non-gallery-app.md)

#### 應用程式 Proxy
##### [顯示應用程式頁面時發生問題](application-proxy-page-appearance-broken-problem.md)
##### [應用程式載入時間過長](application-proxy-page-load-speed-problem.md)
##### [應用程式頁面連結無效](application-proxy-page-links-broken-problem.md)
##### [要開啟哪些適用於應用程式的連接埠](application-proxy-connectivity-ports-how-to.md)
##### [適用於應用程式的連接器群組中沒有運作中的連接器](application-proxy-connectivity-no-working-connector.md)
##### [在管理入口網站中設定](application-proxy-config-how-to.md)
##### [在應用程式中設定單一登入](application-proxy-config-sso-how-to.md)
##### [在管理入口網站中建立應用程式時發生問題](application-proxy-config-problem.md)
##### [設定 Kerberos 限制委派](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
##### [使用 PingAccess 設定](application-proxy-back-end-ping-access-how-to.md)
##### [「無法存取此公司應用程式」錯誤](application-proxy-sign-in-bad-gateway-timeout-error.md)
##### [安裝應用程式 Proxy 代理程式連接器時發生問題](application-proxy-connector-installation-problem.md)


#### 應用程式註冊
##### [應用程式物件的輸入欄位](application-dev-registration-config-specific-application-property-how-to.md)
##### [變更權杖存留期預設值](application-dev-registration-config-change-token-lifetime-how-to.md)

#### 驗證
##### [設定端點](application-dev-registration-config-how-to.md)

#### 條件式存取
##### [客戶不符合裝置註冊預先要求](active-directory-conditional-access.md)
##### [由於條件式存取原則設定不正確，因此封鎖租用戶。](active-directory-conditional-access-device-remediation.md)
##### [離線的公司網路規則會在何時生效，以及如何生效？](https://aka.ms/calocation)
##### [如何增加使用者允許在 Azure AD 中註冊的裝置數目？](active-directory-azureadjoin-setup.md)
##### [如何設定 Exchange Online 的條件式存取？](https://aka.ms/csforexchange)
##### [如何設定適用於 Windows 7 裝置的條件式存取？](active-directory-conditional-access.md#device-based-conditional-access)
##### [哪些應用程式支援條件式存取？](active-directory-conditional-access-supported-apps.md)

#### 尋找 API
##### [尋找 API](application-dev-api-find-an-api-how-to.md)

#### 管理存取
##### [將使用者和群組指派至群組](application-access-assignment-how-to-add-assignment.md)
##### [移除應用程式中的使用者存取](application-access-assignment-how-to-remove-assignment.md)
##### [設定自助式應用程式指派](application-access-self-service-how-to.md)
##### [指派未預期的使用者](application-access-unexpected-user-assignment.md)
##### [應用程式清單中的未預期應用程式](application-access-unexpected-application.md)

#### 多租用戶應用程式
##### [設定新的應用程式](application-dev-setup-multi-tenant-app.md)
##### [新增至應用程式資源庫](application-dev-registration-config-multi-tenant-application-add-to-gallery-how-to.md)

#### 權限
##### [選擇適用於 API 的使用權限](application-dev-perms-for-given-api.md)
##### [將使用權限授與應用程式](application-dev-registration-config-grant-permissions-how-to.md)
##### [委派與應用程式使用權限](application-dev-delegated-and-app-perms.md)
##### [應用程式同意](application-dev-consent-framework.md)

#### 佈建
##### [所需時間](application-provisioning-when-will-provisioning-finish-specific-user.md)
##### [所需時間 - 資源庫應用程式](application-provisioning-when-will-provisioning-finish.md)
##### [設定使用者佈建 - 資源庫應用程式](application-provisioning-config-how-to.md)
##### [設定使用者佈建時發生問題 - 資源庫應用程式](application-provisioning-config-problem.md)
##### [在設定使用者佈建資源庫應用程式的情況下儲存管理員認證時發生問題](application-provisioning-config-problem-storage-limit.md)
##### [未佈建使用者 - 資源庫應用程式](application-provisioning-config-problem-no-users-provisioned.md)
##### [佈建錯誤的使用者 - 資源庫應用程式](application-provisioning-config-problem-wrong-users-provisioned.md)

#### 單一登入
##### [選擇方法](application-config-sso-how-to-choose-sign-on-method.md)
##### [設定](application-dev-registration-config-sso-how-to.md)
##### [設定同盟 - 資源庫應用程式](application-config-sso-how-to-configure-federated-sso-gallery.md)
##### [設定同盟常見問題 - 資源庫應用程式](application-config-sso-problem-configure-federated-sso-gallery.md)
##### [設定同盟 - 非資源庫應用程式](application-config-sso-how-to-configure-federated-sso-non-gallery.md)
##### [設定同盟常見問題 - 非資源庫應用程式](application-config-sso-problem-configure-federated-sso-non-gallery.md)
##### [設定密碼 - 資源庫應用程式](application-config-sso-how-to-configure-password-sso-gallery.md)
##### [設定密碼常見問題 - 資源庫應用程式](application-config-sso-problem-configure-password-sso-gallery.md)
##### [設定密碼 - 非資源庫應用程式](application-config-sso-how-to-configure-password-sso-non-gallery.md)
##### [設定密碼常見問題 - 非資源庫應用程式](application-config-sso-problem-configure-password-sso-non-gallery.md)

#### 使用者登入問題
##### [未預期的同意提示](application-sign-in-unexpected-user-consent-prompt.md)
##### [使用者同意錯誤](application-sign-in-unexpected-user-consent-error.md)
##### [從自訂入口網站登入時發生問題](application-sign-in-other-problem-deeplink.md)
##### [從存取面板登入時發生問題](application-sign-in-other-problem-access-panel.md)
##### [應用程式登入頁面發生錯誤](application-sign-in-problem-application-error.md)
##### [密碼單一登入問題 - 非資源庫應用程式](application-sign-in-problem-password-sso-non-gallery.md)
##### [密碼單一登入問題 - 資源庫應用程式](application-sign-in-problem-password-sso-gallery.md)
##### [登入 Microsoft 應用程式時發生問題](application-sign-in-problem-first-party-microsoft.md)
##### [同盟單一登入問題 - 非資源庫應用程式](application-sign-in-problem-federated-sso-non-gallery.md)
##### [同盟單一登入問題 - 資源庫應用程式](application-sign-in-problem-federated-sso-gallery.md)
##### [自訂開發應用程式問題](application-sign-in-problem-custom-dev.md)
##### [內部部署應用程式問題 - 應用程式 Proxy](application-sign-in-problem-on-premises-application-proxy.md)

### [開發應用程式](active-directory-applications-guiding-developers-for-lob-applications.md)
### [文件庫](active-directory-apps-index.md)

## 管理您的目錄
### [Azure AD Connect](./connect/active-directory-aadconnect.md)
### 自訂網域名稱
#### [快速入門](fundamentals/add-custom-domain.md)
#### [新增自訂網域名稱](users-groups-roles/domains-manage.md)
### [管理目錄](fundamentals/active-directory-administer.md)
### [刪除目錄](users-groups-roles/directory-delete-howto.md)
### [多個目錄](users-groups-roles/licensing-directory-independence.md)
### [自助式註冊](users-groups-roles/directory-self-service-signup.md)
### [接管非受控目錄](users-groups-roles/domains-admin-takeover.md)
### [企業狀態漫遊](active-directory-windows-enterprise-state-roaming-overview.md)
#### [啟用](active-directory-windows-enterprise-state-roaming-enable.md)
#### [群組原則設定](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
#### [Windows 10 設定](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
#### [常見問題集](active-directory-windows-enterprise-state-roaming-faqs.md)
#### [疑難排解](active-directory-windows-enterprise-state-roaming-troubleshooting.md)


### [使用 Azure AD Connect 整合內部部署身分識別](./connect/active-directory-aadconnect.md)

## 委派資源存取
### [管理員角色](users-groups-roles/directory-assign-admin-roles.md)
#### [檢視管理員角色成員](users-groups-roles//directory-manage-roles-portal.md)
#### [將管理員角色指派給使用者](fundamentals/active-directory-users-assign-role-azure-portal.md)
#### [比較成員和來賓使用者的權限](fundamentals/users-default-permissions.md)
### [管理員角色安全性](users-groups-roles/directory-admin-roles-secure.md)  
#### [建立緊急存取管理帳戶](users-groups-roles/directory-emergency-access.md)
### [管理單位](users-groups-roles/directory-administrative-units.md)
### [設定權杖存留期](active-directory-configurable-token-lifetimes.md)

## 存取權檢閱
### [存取權檢閱概觀](active-directory-azure-ad-controls-access-reviews-overview.md)
### [完成存取權檢閱](active-directory-azure-ad-controls-complete-access-review.md)
### [建立存取權檢閱](active-directory-azure-ad-controls-create-access-review.md)
### [開始執行存取權檢閱](active-directory-azure-ad-controls-perform-access-review.md)
### [如何檢閱您的存取權](active-directory-azure-ad-controls-how-to-review-your-access.md)
### [透過存取權檢閱進行來賓存取](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md)
### [透過檢閱管理使用者存取](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md)
### [管理程式和控制項](active-directory-azure-ad-controls-manage-programs-controls.md)
### [取出存取權檢閱結果](active-directory-azure-ad-controls-retrieve-access-review.md)

## 保護您的身分識別
### [條件式存取](active-directory-conditional-access-azure-portal.md)
#### [開始使用](active-directory-conditional-access-azure-portal-get-started.md)
#### 快速入門
##### [設定每個雲端應用程式 MFA](active-directory-conditional-access-app-based-mfa.md)
##### [接受的使用條款](active-directory-conditional-access-tou.md)
##### [偵測到工作階段風險時封鎖存取](active-directory-conditional-access-app-sign-in-risk.md)
#### 教學課程
##### [移轉傳統 MFA 原則](active-directory-conditional-access-migration-mfa.md)
#### 概念
##### [基準保護](active-directory-conditional-access-baseline-protection.md)
##### [條件](active-directory-conditional-access-conditions.md)
##### [位置條件](active-directory-conditional-access-locations.md)
##### [控制項](active-directory-conditional-access-controls.md)
##### [假設工具](active-directory-conditional-access-whatif.md)
##### [了解 Office 365 服務的裝置原則](active-directory-conditional-access-device-policies.md)
#### 使用說明指南
##### [最佳做法](active-directory-conditional-access-best-practices.md)
##### [為來自不信任的網路存取嘗試設定條件式存取原則](active-directory-conditional-access-untrusted-networks.md)
##### [設定裝置型條件式存取](active-directory-conditional-access-policy-connected-applications.md)
##### [設定應用程式型條件式存取](active-directory-conditional-access-mam.md)
##### [為使用者和應用程式提供使用規定](active-directory-tou.md)
##### [移轉傳統原則](active-directory-conditional-access-migration.md)
##### [設定 VPN 連線能力](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/deploy/always-on-vpn-deploy)
##### [設定 SharePoint 和 Exchange Online](active-directory-conditional-access-no-modern-authentication.md)
##### [補救](active-directory-conditional-access-device-remediation.md)
#### [技術參考](active-directory-conditional-access-technical-reference.md)
#### [常見問題集](active-directory-conditional-faqs.md)

### 憑證式驗證
#### [Android](active-directory-certificate-based-authentication-android.md)
#### [iOS](active-directory-certificate-based-authentication-ios.md)
#### [開始使用](active-directory-certificate-based-authentication-get-started.md)

### [Azure AD Identity Protection](active-directory-identityprotection.md)
#### [啟用](active-directory-identityprotection-enable.md)
#### [偵測弱點](active-directory-identityprotection-vulnerabilities.md)
#### [風險事件](active-directory-identity-protection-risk-events.md)
#### [通知](active-directory-identityprotection-notifications.md)
#### [登入體驗](active-directory-identityprotection-flows.md)
#### [模擬風險事件](active-directory-identityprotection-playbook.md)
#### [解除封鎖使用者](active-directory-identityprotection-unblock-howto.md)
#### [常見問題集](active-directory-identity-protection-faqs.md)
#### [詞彙](active-directory-identityprotection-glossary.md)
#### [Microsoft Graph](active-directory-identityprotection-graph-getting-started.md)
### [Privileged Identity Management](privileged-identity-management/pim-configure.md?toc=%2fazure%2factive-directory%2ftoc.json)

## 將其他服務與 Azure AD 整合 
### [將 linkedIn 與 Azure AD 整合](users-groups-roles/linkedin-integration.md)

## [在 Azure 中部署 AD FS](active-directory-aadconnect-azure-adfs.md)
### [高可用性](active-directory-adfs-in-azure-with-azure-traffic-manager.md)
### [變更簽章雜湊演算法](active-directory-federation-sha256-guidance.md)

## [疑難排解](fundamentals/active-directory-troubleshooting-support-howto.md)

## 部署 Azure AD 概念證明 (PoC)
### [PoC 腳本：簡介](active-directory-playbook-intro.md)
### [PoC 腳本：因素](active-directory-playbook-ingredients.md)
### [PoC 腳本：實作](active-directory-playbook-implementation.md)
### [PoC 腳本：建置區塊](active-directory-playbook-building-blocks.md)


# 參考
## [程式碼範例](https://azure.microsoft.com/resources/samples/?service=active-directory)
## [Azure PowerShell Cmdlet](/powershell/azure/overview)
## [Java API 參考](/java/api)
## [.NET API](/active-directory/adal/microsoft.identitymodel.clients.activedirectory)
## [服務限制](users-groups-roles/directory-service-limits-restrictions.md)

# 相關參考
## [Multi-Factor Authentication](/azure/multi-factor-authentication/)
## [Azure AD Connect](./connect/active-directory-aadconnect.md)
## [Azure AD Connect Health](./connect-health/active-directory-aadconnect-health.md)
## [開發人員適用的 Azure AD](./develop/active-directory-how-to-integrate.md)
## [Azure AD Privileged Identity Management](./privileged-identity-management/pim-configure.md)

# 資源
## [Azure 意見反應論壇](https://feedback.azure.com/forums/169401-azure-active-directory)
## [Azure 藍圖](https://azure.microsoft.com/roadmap/?category=security-identity)
## [MSDN 論壇](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
## [定價](https://azure.microsoft.com/pricing/details/active-directory/)
## [定價計算機](https://azure.microsoft.com/pricing/calculator/)
## [服務更新](https://azure.microsoft.com/updates/?product=active-directory)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory)
## [影片](https://azure.microsoft.com/documentation/videos/index/?services=active-directory)
