<script>
import BaseModel from './structure/base-model'
import APP_SETTINGS from '../app-settings'


class DashboardSettingModel extends BaseModel {
  constructor (attributes = {}, collection = null, options = {}) {
    super(attributes, collection, options)
    this.listenPushMessages('settings')
  }
  defaults () {
    return {
      setting_page_title: '',
      setting_items_peer_page: '',
    }
  }
  routes () {
    return {
      fetch: `${ APP_SETTINGS.appApiBaseURL }/dashboard-settings/`,
    }
  }
}

export default {
  model: DashboardSettingModel,
}
</script>
