---
layout: post
workshopID: 22
published: false
---

<style>
 .form {
   padding: 10px;
   width: 80%;
 }
 .privacy {
   padding: 10px;
   margin: 20px 20px 10px 20px;
   background-color: lightgrey;
   font-size: 90%;
 }
 .input {
   width: 100%;
 }

 .form-group {
   padding: 5px;
 }
</style>

Welcome to the MAgPIE22 stories submission page. Please enter your details and shortly describe your proposed contribution to the stories event. For more information please check the [MAgPIE22 stories call for contributions](call).

Acceptance notifications are planned to be send no later than 10th of February 2022. Please contact us via <magpie@pik-potsdam.de> if you have not heard back from us till then or have questions about the procedure.

<form class="form" accept-charset="UTF-8" action="https://rse.pik-potsdam.de/magpie/registration/2022contribution.php" method="POST" enctype="multipart/form-data">

  <div class="form-group">
    <label>Presenter Name<sup>*</sup></label>
    <select name="presenter_title">
       <option></option>
       <option>Ms</option>
       <option>Mr</option>
       <option>Dr</option>
       <option>Prof</option>
    </select>
    <input class="input" type="text" name="first_name" placeholder="First Name" required>
    <input class="input" type="text" name="last_name" placeholder="Last Name" required>
  </div>
  <div class="form-group">
    <label>Email address<sup>*</sup></label>
    <input class="input" type="email" name="email" placeholder="E-Mail" required>
  </div>
  <div class="form-group">
    <label>Affiliation<sup>*</sup></label>
    <input class="input" type="text" name="affiliation" placeholder="Affiliation" required>
  </div>
  <div class="form-group">
    <label>City</label>
    <input class="input" type="text" name="city" placeholder="City" required>
  </div>
  <div class="form-group">
    <label>Country</label>
    <input class="input" type="text" name="country" placeholder="Country" required>
  </div>

  <div class="form-group">
    <label>Contribution Title</label>
    <input class="input" type="text" name="contribution_title" placeholder="Title" required>
  </div>

  <div class="form-group">
    <label>Short description of contribution (max 1000 characters)</label>
    <textarea name="abstract" rows="10" cols="71" maxlength="1000"></textarea>
  </div>

  <div class="privacy">
  <div> Privacy policy statement </div>
  We would like to point out that within this workshop, personal data such as your personal date such as contact details,  will be processed by us. These data are processed exclusively for the purpose of organising your participation in the event and contacting you within the framework of the event. Data minimization and the security of your data as well as your rights as affected persons according to the EU-DSGVO are very important to us. If you have any objections, please contact the <a href="mailto:magpie@pik-potsdam.de">Organising Team</a>. Please read our <a href="https://www.pik-potsdam.de/en/intranet/data-protection-pik/8.2.2Privacypolicyforeventparticipants.pdf">Data protection policy</a> carefully.
  <br/><br/>
  The workshop will be executed based on the teleconferencing system Zoom provided by Zoom Video Communications, Inc. ("Zoom"), 55 Almaden Boulevard, 6th Floor, San Jose, CA 95113. In individual cases, we are planning recordings of speakers. In these cases, we will inform you about it separately. If you participate in discussions, you and your contributions to the discussion by audio, video and/or chat will be a part of the conference session and will be streamed using the Zoom infrastructure and might be transferred to the USA or other Zoom computing centers in the world. We will not do any recordings of participant discussions sessions. At the same time, in deference to the other participants of the conference and to ensure compliance with EU data protection law, we kindly ask you to not film, take photos, record or further publish any material of the conference and the following discussion. Also please do not forward the Zoom link to third parties. Your participation in these Zoom sessions is not required by law or contract. If you do not provide us with your personal data for the purpose of these meetings, you cannot participate in the conference. If your personal data are processed, then you are a data subject within the meaning of the EU-GDPR and you shall have rights vis-à-vis the Controller.
  </div>
  <div class="form-group">
    <input type="checkbox" name="dataprotectionpolicy" required>
    <label>I have read the privacy policy statement and consent to it</label>
  </div>
  <div class="form-group">
    <input type="checkbox" name="publishabstract" required>
    <label>I hereby confirm that my abstract can be published on the conference website.</label>
  </div>

  <button type="submit" class="btn btn-primary">Submit</button>
</form>
