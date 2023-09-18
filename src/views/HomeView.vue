<template>
  <div class="layout-content">
    <div class="title">Discover the Magic of AI in Email Finding</div>
    <div class="block-section">
      <Toast class="toast" position="bottom-right" />
      <div class="flex items-center" v-if="isLoading">
        <ProgressSpinner />
      </div>
      <div class="flex flex-col" v-if="isSubmited">
        <div v-for="(email, index) in sortedEmails" :key="index" class="">
          <div class="flex flex-col sm:flex-row justify-between items-center">
            <div class="text-900 font-medium mb-6 sm:mb-0">{{ email.email }}</div>
            <div class="flex items-center gap-6">
              <Avatar
                :label="email.probability.toString()"
                size="large"
                :style="getProbabilityStyle(email.probability)"
              />
              <Button class="w-32" label="Copy" @click="copyEmail(email.email)" outlined />
            </div>
          </div>

          <Divider />
        </div>
        <div class="flex flex-col-reverse items-center sm:flex-row justify-center gap-6">
          <Button class="w-48" label="Back" @click="resetForms()" outlined />
          <Button class="w-48" label="Copy all emails" @click="copyEmails()" />
        </div>
      </div>
      <div v-if="!isSubmited && !isLoading">
        <TabView>
          <TabPanel header="Manual input">
            <form @submit.prevent="sendManualForm()">
              <div class="grid md:grid-cols-3 grid-cols-1 gap-6 mb-6">
                <div class="flex flex-col gap-2">
                  <label for="firstName">First name</label>
                  <InputText
                    id="firstName"
                    placeholder="Jhon"
                    v-model="manualForm.firstName"
                    :class="{
                      'p-invalid': v$.manualForm.firstName.$error,
                      'p-valid': !v$.manualForm.firstName.$error && v$.manualForm.firstName.$dirty
                    }"
                  />
                  <div
                    class="input-errors"
                    v-for="error of v$.manualForm.firstName.$errors"
                    :key="error.$uid"
                  >
                    <small class="text-red-700">{{ error.$message }}</small>
                  </div>
                </div>
                <div class="flex flex-col gap-2">
                  <label for="lastName">Last name</label>
                  <InputText
                    id="lastName"
                    placeholder="Doe"
                    v-model="manualForm.lastName"
                    :class="{
                      'p-invalid': v$.manualForm.lastName.$error,
                      'p-valid': !v$.manualForm.lastName.$error && v$.manualForm.lastName.$dirty
                    }"
                  />
                  <div
                    class="input-errors"
                    v-for="error of v$.manualForm.lastName.$errors"
                    :key="error.$uid"
                  >
                    <small class="text-red-700">{{ error.$message }}</small>
                  </div>
                </div>
                <div class="flex flex-col gap-2">
                  <label for="jobTitle">Job title</label>
                  <InputText
                    id="jobTitle"
                    placeholder="CEO"
                    v-model="manualForm.jobTitle"
                    :class="{
                      'p-invalid': v$.manualForm.jobTitle.$error,
                      'p-valid': !v$.manualForm.jobTitle.$error && v$.manualForm.jobTitle.$dirty
                    }"
                  />
                  <div
                    class="input-errors"
                    v-for="error of v$.manualForm.jobTitle.$errors"
                    :key="error.$uid"
                  >
                    <small class="text-red-700">{{ error.$message }}</small>
                  </div>
                </div>
              </div>
              <Divider />
              <div class="grid md:grid-cols-2 grid-cols-1 gap-6 mb-8">
                <div class="flex flex-col gap-2">
                  <label for="website">Company Website</label>
                  <InputText
                    id="website"
                    placeholder="example.com"
                    v-model="manualForm.website"
                    :class="{
                      'p-invalid': v$.manualForm.website.$error,
                      'p-valid': !v$.manualForm.website.$error && v$.manualForm.website.$dirty
                    }"
                  />
                  <div
                    class="input-errors"
                    v-for="error of v$.manualForm.website.$errors"
                    :key="error.$uid"
                  >
                    <small class="text-red-700">{{ error.$message }}</small>
                  </div>
                </div>
                <div class="flex flex-col gap-2">
                  <label for="headcount">Company Headcount</label>
                  <Dropdown
                    :options="headcounts"
                    id="headcount"
                    placeholder="Select headcount"
                    v-model="manualForm.headcount"
                    :class="{
                      'p-invalid': v$.manualForm.headcount.$error,
                      'p-valid': !v$.manualForm.headcount.$error && v$.manualForm.headcount.$dirty
                    }"
                  />
                  <div
                    class="input-errors"
                    v-for="error of v$.manualForm.headcount.$errors"
                    :key="error.$uid"
                  >
                    <small class="text-red-700">{{ error.$message }}</small>
                  </div>
                </div>
              </div>
              <div class="grid md:grid-cols-2 md:grid-cols-1 gap-6 mb-8">
                <div class="flex flex-col gap-2">
                  <label for="industry">Company Industry</label>
                  <InputText
                    id="industry"
                    placeholder="Marketing Services"
                    v-model="manualForm.industry"
                    :class="{
                      'p-invalid': v$.manualForm.industry.$error,
                      'p-valid': !v$.manualForm.industry.$error && v$.manualForm.industry.$dirty
                    }"
                  />
                  <div
                    class="input-errors"
                    v-for="error of v$.manualForm.industry.$errors"
                    :key="error.$uid"
                  >
                    <small class="text-red-700">{{ error.$message }}</small>
                  </div>
                </div>
                <div class="flex flex-col gap-2">
                  <label for="location">Company Location</label>
                  <InputText
                    id="location"
                    placeholder="United States"
                    v-model="manualForm.region"
                    :class="{
                      'p-invalid': v$.manualForm.region.$error,
                      'p-valid': !v$.manualForm.region.$error && v$.manualForm.region.$dirty
                    }"
                  />
                  <div
                    class="input-errors"
                    v-for="error of v$.manualForm.region.$errors"
                    :key="error.$uid"
                  >
                    <small class="text-red-700">{{ error.$message }}</small>
                  </div>
                </div>
              </div>

              <div class="flex flex-col-reverse sm:flex-row items-center justify-center gap-6">
                <Button class="w-48" label="Reset fields" @click="resetManualForm()" outlined />
                <Button
                  type="submit"
                  label="Get email"
                  icon="pi pi-angle-right"
                  iconPos="right"
                  class="w-48"
                />
              </div>
            </form>
          </TabPanel>
          <TabPanel header="By linkedin url">
            <form @submit.prevent="sendLinkedinForm()">
              <div class="grid mb-8">
                <div class="flex flex-col gap-2">
                  <label for="linkedin">Linkedin profile url</label>
                  <InputText
                    id="linkedin"
                    placeholder="https://linkedin.com.com/in/example-name"
                    v-model="linkedin"
                    :class="{
                      'p-invalid': v$.linkedin.$error,
                      'p-valid': !v$.linkedin.$error && v$.linkedin.$dirty
                    }"
                  />
                  <div class="input-errors" v-for="error of v$.linkedin.$errors" :key="error.$uid">
                    <small class="text-red-700">{{ error.$message }}</small>
                  </div>
                </div>
              </div>

              <div class="flex justify-center">
                <Button
                  type="submit"
                  label="Get email"
                  icon="pi pi-angle-right"
                  iconPos="right"
                  class="w-48"
                />
              </div>
            </form>
          </TabPanel>
        </TabView>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import InputText from 'primevue/inputtext'
import Dropdown from 'primevue/dropdown'
import Button from 'primevue/button'
import Divider from 'primevue/divider'
import TabView from 'primevue/tabview'
import TabPanel from 'primevue/tabpanel'
import ProgressSpinner from 'primevue/progressspinner'
import Avatar from 'primevue/avatar'
import Toast from 'primevue/toast'
import axios from 'axios'
import { defineComponent } from 'vue'
import { useVuelidate } from '@vuelidate/core'
import { required } from '@vuelidate/validators'

export namespace GetEmails {
  export type Request = {
    firstName: string
    lastName: string
    jobTitle: string
    website: string
    headcount: string
    industry: string
    region: string
  }

  export type Response = {
    email: string
    probability: number
  }[]
}

export default defineComponent({
  components: {
    InputText,
    Dropdown,
    Divider,
    Button,
    TabView,
    TabPanel,
    ProgressSpinner,
    Avatar,
    Toast
  },
  setup() {
    const headcounts = ['1-10', '11-50', '51-200', '201-500', '501-1000', '1001-5000']

    return { headcounts, v$: useVuelidate() }
  },

  data: () => ({
    manualForm: {
      firstName: '',
      lastName: '',
      jobTitle: '',
      website: '',
      headcount: '',
      industry: '',
      region: ''
    },
    linkedin: '',
    isSubmited: false,
    isLoading: false,
    emails: [] as GetEmails.Response
  }),

  validations() {
    return {
      manualForm: {
        firstName: { required },
        lastName: { required },
        jobTitle: { required },
        website: { required },
        headcount: { required },
        industry: { required },
        region: { required }
      },
      linkedin: { required }
    }
  },

  computed: {
    sortedEmails(): GetEmails.Response {
      const emails = this.$data.emails

      const sortedEmails = emails.sort((a, b) => b.probability - a.probability)

      const normalizedEmails = sortedEmails.map((email) => {
        const probability = Math.round(email.probability)
        return { email: email.email, probability }
      })

      return normalizedEmails
    }
  },

  methods: {
    async sendManualForm() {
      this.v$.manualForm.$touch()

      if (!this.v$.manualForm.$errors.length) {
        const data = {} as GetEmails.Request

        Object.entries(this.$data.manualForm).forEach(
          //@ts-ignore
          ([key, value]) => (data[key] = value)
        )

        const emails = await this.getEmails(data)

        if (emails) {
          this.emails = emails
          this.isSubmited = true
        }
      }
    },

    async sendLinkedinForm() {
      this.v$.linkedin.$touch()

      if (!this.v$.linkedin.$errors.length) {
        const linkedin = this.$data.linkedin

        try {
          this.isLoading = true

          const lead = (
            await axios.post(
              'https://api.generect.com/api/linkedin/leads/by_link/',
              { url: linkedin },
              {
                headers: {
                  Authorization: `Token d9874cb73bd9800df6471cd043c9fa0b4823245a`
                }
              }
            )
          ).data.lead

          if (lead.company_url) {
            const company = (
              await axios.post(
                'https://api.generect.com/api/linkedin/companies/by_link/',
                { url: lead.company_url },
                {
                  headers: {
                    Authorization: `Token d9874cb73bd9800df6471cd043c9fa0b4823245a`
                  }
                }
              )
            ).data.company

            const data: GetEmails.Request = {
              firstName: lead.first_name,
              lastName: lead.last_name,
              jobTitle: lead.job_title,
              website: company.domain,
              headcount: company.headcount_range,
              industry: lead.company_industry,
              region: lead.location
            }

            const emails = await this.getEmails(data)

            if (emails) {
              this.emails = emails
              this.isSubmited = true
            }
          } else {
            this.$toast.add({
              severity: 'error',
              summary: `Company error`,
              detail: `The user does not have a company`,
              life: 3000
            })
          }
        } catch (error: any) {
          console.error(error)
        } finally {
          this.isLoading = false
        }
      }
    },

    resetManualForm() {
      Object.keys(this.$data.manualForm).forEach(
        //@ts-ignore
        (key) => (this.$data.manualForm[key] = '')
      )
    },

    resetLinkedinForm() {
      this.linkedin = ''
    },

    resetForms() {
      this.resetManualForm()
      this.resetLinkedinForm()
      this.emails = []
      this.isSubmited = false
      this.v$.manualForm.$reset()
      this.v$.linkedin.$reset()
    },

    async getEmails(request: GetEmails.Request): Promise<GetEmails.Response | undefined> {
      this.isLoading = true

      try {
        const api = axios.create({
          baseURL: 'https://getemail-api-dq87w.ondigitalocean.app'
        })

        const { data } = await api.post('emails', request)

        if (data.data) return data.data
        if (data.results) return data.results

        return data
      } catch (error: any) {
        console.error(error)
      } finally {
        this.isLoading = false
      }
    },

    copyEmails() {
      const emailsArray = this.sortedEmails.map((email) => email.email)
      const emailsString = emailsArray.join(', ')

      navigator.clipboard.writeText(emailsString)
      this.$toast.add({
        severity: 'success',
        summary: 'Emails copied',
        detail: `${emailsArray.length} emails were copied to clipboard`,
        life: 3000
      })
    },

    copyEmail(email: string) {
      navigator.clipboard.writeText(email)
      this.$toast.add({
        severity: 'success',
        summary: 'Email copied',
        detail: `${email} was copied to clipboard`,
        life: 3000
      })
    },

    getProbabilityStyle(number: number) {
      if (number >= 95) return { 'background-color': '#3B82F6', color: '#ffffff' }
      if (number >= 80) return { 'background-color': '#4caf4f', color: '#ffffff' }
      if (number >= 50) return { 'background-color': '#F59E0B', color: '#ffffff' }
      else return { 'background-color': '#EF4444', color: '#ffffff' }
    }
  }
})
</script>

<style scoped>
.toast {
  left: 20px !important;
  width: unset !important;
}

.p-valid {
  border-color: #22c55e;
}
.layout-content {
  display: flex;
  align-items: center;
  flex-direction: column;
  padding: 2rem 1rem;
}

.surface-border {
  border-color: var(--surface-border) !important;
}

.title {
  text-align: center;
  font-size: 2rem;
  font-weight: 700;
  margin-bottom: 1rem;
}

.block-section {
  max-width: 900px;
  width: 100%;
  padding: 2rem;
  border: 1px solid var(--surface-border);
  border-radius: 4px;
}
</style>
