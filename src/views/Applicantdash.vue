<template>
  <div class="container">
    <div class="sidebar">
      <img src="/public/gcchnobg.png" alt="GCCH Logo" class="logo" />
      <ul>
        <li style="font-weight: bold">
          <router-link to="/Applicantdash" class="sidenav-text">
            <img src="/public/job-offer.png" class="ikon" />
            JOB LISTINGS
          </router-link>
        </li>
        <li>
          <router-link to="/Applicantmessage" class="sidenav-text">
            <img src="/public/mail.png" class="ikon" /> MESSAGES
          </router-link>
        </li>
        <li>
          <router-link to="/Applicantprofile" class="sidenav-text">
            <img src="/public/user.png" class="ikon" />
            PROFILE
          </router-link>
        </li>
      </ul>
      <button class="sign-out" @click="toggleSignOut">
        <img src="/public/logout.png" alt="Sign Out Icon" />
      </button>

      <div v-if="showSignOut" class="popup-overlay" @click.self="toggleSignOut">
        <div class="popup signout-popup">
          <h3>⚠️ Sign Out</h3>
          <p>Are you sure you want to sign out?</p>
          <div class="signout-actions">
            <button class="cancel-btn" @click="toggleSignOut">Cancel</button>
            <button class="signout-btn" @click="confirmSignOut">
              Sign Out
            </button>
          </div>
        </div>
      </div>
    </div>
    <div class="main">
      <div class="topbar">
        <div class="left-top">
          <img class="avatar" src="/public/user.png" alt="Avatar" />
          <input type="text" placeholder="Search..." />
        </div>
        <div class="icons-right">
          <div class="icon industry-dropdown">
            <img src="/public/search.png" />
            <div class="custom-dropdown">
              <button @click="clearFilters" class="clear-btn">
                Clear Filters
              </button>

              <div class="dropdown-label">Industry</div>
              <ul class="dropdown-options">
                <li @click="selectIndustry('Techfield')">Tech Field</li>
                <li @click="selectIndustry('Hospitality & Tourism Management')">
                  Hospitality & Tourism Management
                </li>
                <li @click="selectIndustry('Education, Arts & Sciences')">
                  Education, Arts & Sciences
                </li>
                <li @click="selectIndustry('Business & Accountancy')">
                  Business & Accountancy
                </li>
                <li @click="selectIndustry('Health Profession')">
                  Health Profession
                </li>
              </ul>
            </div>
          </div>

          <div class="icon industry-dropdown">
            <img src="/public/company.png" />
            <div class="custom-dropdown">
                          <button @click="clearFilters" class="clear-btn">Clear Filters</button>

              <div class="dropdown-label">Company</div>
              <ul class="dropdown-options">
                <li @click="selectCompany('Tech Solutions Inc.')">
                  Tech Solutions Inc.
                </li>
                <li @click="selectCompany('Tech Solutions Inc.')">
                  Barbatos Co.
                </li>
                <li @click="selectCompany('Tech Solutions Inc.')">Lopez Co.</li>
                <li @click="selectCompany('Tech Solutions Inc.')">Dior</li>
                <li @click="selectCompany('Tech Solutions Inc.')">
                  Louis Vuitton
                </li>
              </ul>
            </div>
          </div>

          <div class="icon" @click="toggleMail">
            <img src="/public/mail.png" />
            <span v-if="unreadMessages > 0">{{ unreadMessages }}</span>
          </div>
          <div class="icon" @click="toggleNotif">
            <img src="/public/notification.png" />
            <span v-if="newNotifications > 0">{{ newNotifications }}</span>
          </div>
        </div>

        <div v-if="showMail" class="popup-overlay" @click.self="toggleMail">
          <div class="popup">
            <h3>📬 Messages</h3>
            <ul class="popup-list">
              <li>John: Interested in your post.</li>
              <li>Jane: Sent a resume for the job.</li>
              <li>Mark: Asking about job requirements.</li>
            </ul>
            <button @click="toggleMail">Close</button>
          </div>
        </div>

        <div v-if="showNotif" class="popup-overlay" @click.self="toggleNotif">
          <div class="popup">
            <h3>🔔 Notifications</h3>
            <ul class="popup-list">
              <li>3 new applicants this week</li>
              <li>Company profile approved</li>
              <li>New feature: Analytics tab</li>
            </ul>
            <button @click="toggleNotif">Close</button>
          </div>
        </div>
      </div>
      <div class="content">
        <div class="left-content">
          <div class="job-box" v-for="job in filteredJobs" :key="job.id">
            <div class="job-card">
              <!-- Header -->
              <div class="job-header">
                <img src="/public/user.png" class="ikon" />
                <h3 class="company-name">{{ job.company }}</h3>
                <!-- <button class="message-btn" @click="sendMessage(job.company)">
                  Send Message
                </button>
                <button class="apply-btn" @click="applyToJob(job.id)">
                  Apply
                </button> -->
              </div>

              <!-- Move salary here BELOW description -->
              <div class="job-info">
                <div class="job-detail">
                  <img src="/public/briefcase.png" class="ikon" />
                  <span class="job-type">{{ job.type }}</span>
                </div>
              </div>

              <div class="job-info">
                <div class="job-detail">
                  <img src="/public/money.png" class="ikon" />
                  <span class="salary">₱{{ job.salary }}</span>
                </div>
              </div>
              <!-- Job Description -->
              <p class="job-description">{{ job.description }}</p>

              <!-- Optional Media -->
              <div v-if="job.media" class="media-container">
                <img :src="job.media" alt="Job Media" class="job-media" />
              </div>

              <!-- Apply and Message Buttons -->
              <div class="job-actions">
                <button class="message-btn" @click="sendMessage(job.company)">
                  Send Message
                </button>
                <button class="apply-btn" @click="applyToJob(job.id)">
                  Apply
                </button>
              </div>
            </div>
          </div>
        </div>

        <div class="right-content">
          <h3>CHECK THIS OUT</h3>
          <div class="updates-list">
            <div
              v-for="(update, index) in updates"
              :key="index"
              class="update-box"
            >
              <h2>
                <span v-if="update.type === 'message'"></span>
                <span v-if="update.type === 'applicant'"></span>
                <span v-if="update.type === 'job_post'"></span>
                {{ update.user }} {{ update.content }}
              </h2>
              <p>{{ update.time }}</p>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>

  <div
    v-if="showApplyPopup"
    class="popup-overlay"
    @click.self="closeApplyPopup"
  >
    <div class="popup">
      <h3>📄 Upload Your Resume</h3>
      <input type="file" @change="handleFileUpload" accept=".pdf,.doc,.docx" />
      <br /><br />
      <button @click="submitApplication">Apply</button>
      <button
        style="margin-left: 10px; background-color: gray"
        @click="closeApplyPopup"
      >
        Cancel
      </button>
    </div>
  </div>
  <div
    v-if="showMessagePopup"
    class="popup-overlay"
    @click.self="showMessagePopup = false"
  >
    <div class="popup">
      <h3>✉️ Message to {{ selectedCompany }}</h3>
      <textarea
        v-model="messageContent"
        placeholder="Type your message here..."
        rows="5"
        style="width: 100%; padding: 8px"
      ></textarea>
      <br /><br />
      <button @click="sendActualMessage">Send</button>
      <button
        style="margin-left: 10px; background-color: gray"
        @click="showMessagePopup = false"
      >
        Cancel
      </button>
    </div>
  </div>
</template>

<script setup>
import { ref, reactive, computed, onMounted } from 'vue';
import { useRouter } from 'vue-router';
import axios from 'axios';

const router = useRouter();

// Reactive state variables
const showMail = ref(false);
const showNotif = ref(false);
const showSignOut = ref(false);
const unreadMessages = ref(0);
const newNotifications = ref(0);
const showApplyPopup = ref(false);
const selectedJobId = ref(null);
const resumeFile = ref(null);
const showMessagePopup = ref(false);
const selectedIndustry = ref(null);
const selectedCompany = ref(null);
const messageContent = ref("");

// Jobs data
const jobs = reactive([
  {
    id: 1,
    company: "Tech Solutions Inc.",
    description:
      "Hi idol! Walang signal dito sa bukid pero nung nalaman kong nag post ka dali dali akong bumaba ng bukid, tumawid ako ng tatlong ilog, tinumbok ko ang pitong bundok, at umutang ako ng perang pamasahe papuntang syudad at namalimos pa ako para may pang hulog sa pisonet para lang maka react sa post mo.",
    type: "Education, Arts & Sciences",
    salary: "30,000 - 40,000",
    media: "/public/vincent.png",
  },
  {
    id: 2,
    company: "Green Valley Farms",
    description:
      "Hiring for a farm manager with experience in organic farming...",
    type: "Education, Arts & Sciences",
    salary: "20,000 - 25,000",
    media: "/public/vincent.png",
  },
  {
    id: 3,
    company: "Creative Minds Agency",
    description: "Seeking a graphic designer with a strong portfolio...",
    type: "Techfield",
    salary: "15,000 - 20,000",
    media: "/public/vincent.png",
  },
]);

// Updates data
const updates = reactive([
  {
    type: "message",
    user: "Jape",
    time: "10:47 AM",
    content: "sent you a message",
  },
  {
    type: "applicant",
    user: "Paulo",
    time: "9:15 AM",
    content: "submitted a resume",
  },
  {
    type: "job_post",
    user: "You",
    time: "8:00 AM",
    content: "posted a job ",
  },
]);

// Computed filteredJobs
const filteredJobs = computed(() => {
  return jobs.filter((job) => {
    const matchesIndustry = selectedIndustry.value
      ? job.type === selectedIndustry.value
      : true;
    const matchesCompany = selectedCompany.value
      ? job.company === selectedCompany.value
      : true;
    return matchesIndustry && matchesCompany;
  });
});

// Methods (just normal functions now)
function toggleMail() {
  showMail.value = !showMail.value;
  if (showMail.value) {
    unreadMessages.value = 0;
  }
}

function toggleNotif() {
  showNotif.value = !showNotif.value;
  if (showNotif.value) {
    newNotifications.value = 0;
  }
}

function toggleSignOut() {
  showSignOut.value = !showSignOut.value;
}

function confirmSignOut() {
  axios.post('logout')
    .then((response) => {
      console.log("Sign out successful:", response.data.message);
      router.push("/login");
    })
    .catch((error) => {
      console.error("Error signing out:", error);
    });
};

function postJob() {
  const text = document.querySelector("textarea").value;
  fetch("/post-job", {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
    },
    body: JSON.stringify({ text }),
  })
    .then((response) => response.json())
    .then((data) => console.log(data))
    .catch((error) => console.error(error));
}

function filterBy(industry) {
  console.log("Selected Industry:", industry);
}

function applyToJob(jobId) {
  selectedJobId.value = jobId;
  showApplyPopup.value = true;
}

function submitApplication() {
  if (!resumeFile.value) {
    alert("Please upload your resume.");
    return;
  }

  console.log("Applying to job ID:", selectedJobId.value);
  console.log("Resume file:", resumeFile.value.name);

  const formData = new FormData();
  formData.append("resume", resumeFile.value);
  formData.append("jobId", selectedJobId.value);

  // You can add fetch or axios call here to submit formData to server

  showApplyPopup.value = false;
  resumeFile.value = null;
  selectedJobId.value = null;
}

function handleFileUpload(event) {
  resumeFile.value = event.target.files[0];
}

function closeApplyPopup() {
  showApplyPopup.value = false;
  resumeFile.value = null;
  selectedJobId.value = null;
}

function sendMessage(company) {
  selectedCompany.value = company;
  showMessagePopup.value = true;
}

function sendActualMessage() {
  console.log("Sending message to", selectedCompany.value);
  console.log("Message content:", messageContent.value);
  showMessagePopup.value = false;
  messageContent.value = "";
}

function selectIndustry(industry) {
  selectedIndustry.value = industry;
}

function selectCompany(company) {
  selectedCompany.value = company;
}

function clearFilters() {
  selectedIndustry.value = null;
  selectedCompany.value = null;
}

// Lifecycle hook
onMounted(() => {
  setInterval(() => {
    unreadMessages.value += 1;
    newNotifications.value += 1;
  }, 20000);
});
</script>


<style scoped>
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
  font-family: work-sans, sans-serif;
}
body,
.container {
  display: flex;
  background: #e6f0ea;
  height: 100vh;
  overflow: hidden;
}
.sidebar {
  width: 200px;
  background: #fafafa;
  padding: 20px 0;
  border-right: 1px solid #ccc;
  flex-shrink: 0;
}

.logo {
  height: 8vh;
  width: 14vh;
  margin-left: 5.5vh;
  margin-bottom: 15vh;
}

.sidebar ul {
  list-style: none;
  padding: 0;
}
.sidebar ul li {
  padding: 20px 20px;
  margin-bottom: 10px;
  gap: 13px;
  cursor: pointer;
  display: flex;
  align-items: center;
  border-radius: 10px;
  transition: all 0.3s ease-in-out;
}

.sidebar ul li:hover {
  color: #045d56;
  transform: translateY(-3px);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
  background-color: #f0f0f0;
}

.sidenav-text {
  text-decoration: none;
  color: inherit;
  cursor: pointer;
}

.sign-out {
  display: flex;
  align-items: center;
  cursor: pointer;
  transition: transform 0.3s ease, background-color 0.3s ease;
  padding: 15px 20px;
  margin-top: 32.5vh;
  margin-left: 8.5vh;
  border-radius: 10px;
}

.sign-out img {
  width: 20px;
  height: 20px;
}

.sign-out:hover {
  background-color: #e0f2f1;
  transform: translateX(5px);
  color: #045d56;
}

.signout-popup {
  text-align: center;
}

.signout-popup p {
  margin: 10px 0 20px;
}

.signout-actions {
  margin-top: 15px;
  display: flex;
  justify-content: flex-end;
  gap: 10px;
}

.cancel-btn {
  background-color: gray;
  color: white;
  padding: 8px 16px;
  border: none;
  border-radius: 8px;
  cursor: pointer;
}

.cancel-btn:hover {
  background-color: #555;
}

.signout-btn {
  background-color: #d32f2f;
  color: white;
  padding: 8px 16px;
  border: none;
  border-radius: 8px;
  cursor: pointer;
}

.signout-btn:hover {
  background-color: #b71c1c;
}

.ikon {
  width: 20px;
  height: 20px;
  margin-right: 10px;
}
.main {
  flex-grow: 1;
  display: flex;
  flex-direction: column;
  height: 100vh;
  overflow: hidden;
}
.topbar {
  height: 60px;
  background: #fafafa;
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 0 20px;
  border-bottom: 1px solid #ccc;
  flex-shrink: 0;
}
.left-top {
  display: flex;
  align-items: center;
  gap: 10px;
}
.avatar {
  width: 40px;
  height: 40px;
  background: #ffffff;
  border-radius: 50%;
}
.topbar input[type="text"] {
  padding: 8px 10px;
  border: 1px solid #ccc;
  border-radius: 20px;
  width: 300px;
}

.popup-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.4);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}

.popup {
  background: white;
  padding: 25px;
  border-radius: 15px;
  width: 28%;
  max-height: 400px;
  overflow-y: auto;
  text-align: left;
  box-shadow: 0 8px 20px rgba(0, 0, 0, 0.2);
  animation: popIn 0.3s ease;
}

.popup h3 {
  margin-bottom: 15px;
  font-size: 22px;
  color: #045d56;
}

.popup-list {
  list-style-type: none;
  padding: 0;
  margin-bottom: 20px;
}

.popup-list li {
  padding: 10px 0;
  border-bottom: 1px solid #ddd;
}

.popup button {
  background-color: #045d56;
  color: white;
  padding: 8px 16px;
  border: none;
  border-radius: 8px;
  cursor: pointer;
}

.popup button:hover {
  background-color: #033f3a;
}

@keyframes popIn {
  from {
    opacity: 0;
    transform: scale(0.95);
  }
  to {
    opacity: 1;
    transform: scale(1);
  }
}

.content {
  flex-grow: 1;
  overflow-y: auto;
  padding: 20px;
  display: flex;
  gap: 20px;
  margin-bottom: 1vh;
}

.left-content {
  flex: 3;
}

.company-name {
  font-size: 20px;
  font-weight: bold;
}

.apply-btn {
  background-color: #045d56;
  color: #fff;
  border: none;
  margin-left: auto;
  padding: 6px 12px;
  border-radius: 6px;
  cursor: pointer;
  transition: all 0.3s ease-in-out;
  transform: scale(1);
}

.apply-btn:hover {
  background-color: #f1f1f1;
  color: #045d56;
  transform: scale(1.08);
}

.message-btn {
  background-color: #045d56;
  color: #fff;
  width: 15%;
  margin-left: 55vh;
  border: none;
  padding: 6px 10px;
  border-radius: 6px;
  cursor: pointer;
  transition: all 0.3s ease-in-out;
  transform: scale(1);
}

.message-btn:hover {
  background-color: #f1f1f1;
  color: #045d56;
  transform: scale(1.08);
}
.job-box {
  border: 1px solid #ddd;
  padding: 15px;
  max-width: 100%;
  margin-bottom: 20px;
  border-radius: 12px;
  background-color: #fff;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.05);
}
.job-card {
  border: 1px solid #ddd;
  padding: 16px;
  border-radius: 8px;
  margin-bottom: 20px;
  background-color: #fff;
}
.job-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.job-description {
  word-wrap: break-word;
  font-size: 15px;
  margin-top: 3vh;
  margin-bottom: 3vh;
  margin-left: 10vh;
  margin-right: 10vh;
  line-height: 1.1;
  font-size: 18px;
  text-align: justify;
  text-indent: 2em;
}

.job-media {
  max-width: 100%;
  max-height: 60vh;
  margin-left: 25vh;
  margin-bottom: 10px;
}

.job-type,
.salary {
  font-weight: 500;
}
.job-info {
  display: flex;
  margin-top: 10px;
}

.job-detail {
  display: flex;
  margin-right: 5vh;
  align-items: center;
  gap: 5px;
}

label {
  background-color: #045d56;
  color: #fff;
  padding: 8px 10px;
  margin-top: 10px;
  border: none;
  border-radius: 10px;
  cursor: pointer;
  margin-right: 70%;
  transition: color 0.5s ease-in-out;
}

.right-content {
  flex: 1;
  background: white;
  border-radius: 10px;
  padding: 20px;
  height: fit-content;
}
.icons-right {
  display: flex;
  gap: 20px;
  align-items: center;
}
.icon {
  position: relative;
  cursor: pointer;
}
.icon img {
  width: 25px;
  height: 25px;
}

.icon span {
  font-size: 12px;
  position: absolute;
  top: -10px;
  right: -10px;
  background: red;
  color: white;
  border-radius: 50%;
  padding: 2px 5px;
}

.custom-dropdown {
  position: absolute;
  top: 30px;
  right: 10%;
  background: #fff;
  border: 1px solid #ccc;
  border-radius: 8px;
  display: none;
  width: 200px;
  z-index: 10;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

.custom-dropdown .dropdown-label {
  padding: 8px 12px;
  background-color: #045d56;
  color: white;
  border-top-left-radius: 8px;
  border-top-right-radius: 8px;
  font-weight: bold;
}

.custom-dropdown .dropdown-options {
  list-style: none;
  margin: 0;
  padding: 0;
}

.custom-dropdown .dropdown-options li {
  padding: 10px 12px;
  cursor: pointer;
  transition: background 0.3s ease;
}

.custom-dropdown .dropdown-options li:hover {
  background-color: #e0f2f1;
}

.industry-dropdown:hover .custom-dropdown {
  display: block;
}

.update-box {
  background-color: #f4f8fc;
  border-bottom: 4px solid #045d56;
  border-radius: 1vh;
  padding: 20px;
  font-size: 10px;
  margin-top: 2vh;
  margin-bottom: 20px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
  transition: transform 0.3s ease, box-shadow 0.3s ease;
}

.update-box:hover {
  transform: translateY(-5px);
  box-shadow: 0 10px 15px rgba(0, 0, 0, 0.2);
}
</style>
