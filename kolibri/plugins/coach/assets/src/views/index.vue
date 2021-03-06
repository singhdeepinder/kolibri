<template>

  <div>
    <core-base
      :immersivePage="currentPageIsImmersive"
      :topLevelPageName="topLevelPageName"
      :appBarTitle="appBarTitle"
      :immersivePageIcon="immersivePageIcon"
      :immersivePageRoute="toolbarRoute"
      :immersivePagePrimary="immersivePagePrimary"
    >

      <template v-if="showCoachNav">
        <nav-title
          :className="className"
          :classId="classId"
          :username="usernameForCurrentScope"
        />
        <top-nav class="top-nav" />
      </template>

      <!-- TODO need a better solution for passing in authMessage -->
      <component authorizedRole="adminOrCoach" :is="currentPage" />

    </core-base>
  </div>

</template>


<script>

  import { PageNames } from '../constants';
  import { UserScopes } from '../constants/reportConstants';
  import { className } from '../state/getters/main';
  import { isAdmin, isCoach, isSuperuser } from 'kolibri.coreVue.vuex.getters';
  import { TopLevelPageNames } from 'kolibri.coreVue.vuex.constants';
  import authMessage from 'kolibri.coreVue.components.authMessage';
  import topNav from './top-nav';
  import classListPage from './class-list-page';
  import examsPage from './exams/exams-page';
  import createExamPage from './exams/create-exam-page';
  import examReportPage from './exams/exam-report-page';
  import examReportDetailPage from './exams/exam-report-detail-page';
  import groupsPage from './groups-page';
  import coreBase from 'kolibri.coreVue.components.coreBase';
  import learnerExerciseDetailPage from './reports/learner-exercise-detail-page';
  import recentItemsPage from './reports/recent-items-page';
  import channelListPage from './reports/channel-list-page';
  import itemListPage from './reports/item-list-page';
  import learnerListPage from './reports/learner-list-page';
  import navTitle from './nav-title';
  import { LessonsPageNames } from '../constants/lessonsConstants';
  import LessonsRootPage from './lessons/LessonsRootPage';
  import LessonSummaryPage from './lessons/LessonSummaryPage';
  import LessonResourceSelectionPage from './lessons/LessonResourceSelectionPage';
  import LessonContentPreviewPage from './lessons/LessonContentPreviewPage';
  import LessonResourceUserReportPage from './reports/learner-exercise-detail-page/learner-exercise-report';
  import LessonResourceUserSummaryPage from './lessons/LessonResourceUserSummaryPage';

  // IDEA set up routenames that all use the same PageName instead of doing this?
  // See Content Preview routes in app.js + PageName handling here
  const selectionPages = [LessonsPageNames.SELECTION, LessonsPageNames.SELECTION_ROOT];
  const resourceUserPages = [
    LessonsPageNames.RESOURCE_USER_SUMMARY,
    LessonsPageNames.RESOURCE_USER_REPORT,
  ];

  const immersivePages = [
    ...selectionPages,
    ...resourceUserPages,
    LessonsPageNames.CONTENT_PREVIEW,
    LessonsPageNames.RESOURCE_CLASSROOM_REPORT,
  ];

  const pageNameToComponentMap = {
    [PageNames.CLASS_LIST]: classListPage,
    [PageNames.EXAMS]: examsPage,
    [PageNames.GROUPS]: groupsPage,
    [PageNames.CREATE_EXAM]: createExamPage,
    // reports
    [PageNames.RECENT_CHANNELS]: channelListPage,
    [PageNames.RECENT_ITEMS_FOR_CHANNEL]: recentItemsPage,
    [PageNames.RECENT_LEARNERS_FOR_ITEM]: learnerListPage,
    [PageNames.RECENT_LEARNER_ITEM_DETAILS]: learnerExerciseDetailPage,
    [PageNames.TOPIC_CHANNELS]: channelListPage,
    [PageNames.TOPIC_CHANNEL_ROOT]: itemListPage,
    [PageNames.TOPIC_ITEM_LIST]: itemListPage,
    [PageNames.TOPIC_LEARNERS_FOR_ITEM]: learnerListPage,
    [PageNames.TOPIC_LEARNER_ITEM_DETAILS]: learnerExerciseDetailPage,
    [PageNames.LEARNER_LIST]: learnerListPage,
    [PageNames.LEARNER_CHANNELS]: channelListPage,
    [PageNames.LEARNER_CHANNEL_ROOT]: itemListPage,
    [PageNames.LEARNER_ITEM_LIST]: itemListPage,
    [PageNames.LEARNER_ITEM_DETAILS]: learnerExerciseDetailPage,
    [PageNames.EXAM_REPORT]: examReportPage,
    [PageNames.EXAM_REPORT_DETAIL]: examReportDetailPage,
    // lessons
    [LessonsPageNames.ROOT]: LessonsRootPage,
    [LessonsPageNames.SUMMARY]: LessonSummaryPage,
    [LessonsPageNames.SELECTION_ROOT]: LessonResourceSelectionPage,
    [LessonsPageNames.SELECTION]: LessonResourceSelectionPage,
    [LessonsPageNames.CONTENT_PREVIEW]: LessonContentPreviewPage,
    [LessonsPageNames.RESOURCE_USER_SUMMARY]: LessonResourceUserSummaryPage,
    [LessonsPageNames.RESOURCE_USER_REPORT]: LessonResourceUserReportPage,
  };

  export default {
    name: 'coachIndexPage',
    $trs: {
      coachToolbarHeader: 'Coach',
      selectPageToolbarHeader: 'Select resources',
      resourceUserPageToolbarHeader: 'Lesson Report Details',
      previewContentPageToolbarHeader: 'Preview resources',
    },
    components: {
      authMessage,
      topNav,
      coreBase,
      navTitle,
    },
    computed: {
      topLevelPageName: () => TopLevelPageNames.COACH,
      currentPage() {
        if (!this.userCanAccessPage) {
          // TODO better solution
          return 'authMessage';
        }
        return pageNameToComponentMap[this.pageName] || null;
      },
      showCoachNav() {
        return (
          this.pageName !== PageNames.CLASS_LIST &&
          this.userCanAccessPage &&
          !this.currentPageIsImmersive
        );
      },
      currentPageIsImmersive() {
        return immersivePages.includes(this.pageName);
      },
      userCanAccessPage() {
        return this.isCoach || this.isAdmin || this.isSuperuser;
      },
      appBarTitle() {
        if (this.currentPageIsImmersive) {
          if (this.pageName === LessonsPageNames.CONTENT_PREVIEW) {
            return this.$tr('previewContentPageToolbarHeader');
          }
          if (selectionPages.includes(this.pageName)) {
            return this.$tr('selectPageToolbarHeader');
          }
          if (resourceUserPages.includes(this.pageName)) {
            return this.$tr('resourceUserPageToolbarHeader');
          }
        }
        return this.$tr('coachToolbarHeader');
      },
      immersivePageIcon() {
        const backButtonPages = [LessonsPageNames.CONTENT_PREVIEW, ...resourceUserPages];
        if (backButtonPages.includes(this.pageName)) {
          return 'arrow_back';
        }
        return 'close';
      },
      immersivePagePrimary() {
        // TODO going to need to set a backgrund color
        if (this.pageName === LessonsPageNames.CONTENT_PREVIEW) {
          return false;
        }
        return true;
      },
      usernameForCurrentScope() {
        if (this.pageState.userScope === UserScopes.USER) {
          return this.pageState.userScopeName;
        }
        return null;
      },
    },
    vuex: {
      getters: {
        toolbarRoute: state => state.pageState.toolbarRoute,
        pageName: state => state.pageName,
        isAdmin,
        isCoach,
        isSuperuser,
        className,
        classList: state => state.classList,
        classId: state => state.classId,
        isLoading: state => state.core.loading,
        pageState: state => state.pageState,
      },
    },
  };

</script>


<style lang="stylus" scoped>

  .top-nav
    margin-bottom: 32px

</style>
