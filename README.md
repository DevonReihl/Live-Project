####Live Project

My last three weeks as tech academy I was blessed with the opportunity to work on a real live project. This gave me exposure to project management and team work. I was working with a team of 8-10 other people on an ASP.NET MVC Entity-Framework Code First App. Because the framework was already established I did not have any HTML I was about to do but I got to work on both Front end and back end stories during the three sprints of the project. I also stumbled into quite a few bugs and other learning things not directly related to my storied of code. 
Below I will should you some of the code I did in Front End, Back End as well as one of the bugs. 

##Front End

Throughout my time on Live Project I was able to do varies little stories to improve the look of the website like adding CSS (or removing for that matter) to make columns look the same height despite the content within them as well as width so it looked nice and uniform. However the thing I did that was the biggest change was adding a Javascript page preloader animation.

`<body>
    @*ADDED PRELOAD ANIMATION*@
    <div class="lds-hourglass"></div>
    @if (User.Identity.IsAuthenticated)
    {

@keyframes lds-hourglass {
    0% {
        transform: rotate(0);
        animation-timing-function: cubic-bezier(0.55, 0.055, 0.675, 0.19);
    }

    50% {
        transform: rotate(900deg);
        animation-timing-function: cubic-bezier(0.215, 0.61, 0.355, 1);
    }

    100% {
        transform: rotate(1800deg);
    }
}`

This code worked wonderfully but the project manager did not implement it do to a conflict with the hamburger navigation bar and there was unfortunately not enough time left for me to fix the conflict.
I also returned and removed functionality of buttons to the Company News views.

`@foreach (var item in Model)
                {
                    <tr>
                        <td>
                            @Html.DisplayFor(modelItem => item.Title)
                        </td>
                        <td>
                            @Html.DisplayFor(modelItem => item.NewsItem)
                        </td>
                        <td>
                            @Convert.ToDateTime(item.ExpirationDate).ToString("MM-dd-yyyy")
                        </td>
                        <td>
                            @*RETURN EDIT AND DELETE BUTTONS TO VIEW*@
                            @Html.Partial(AnchorButtonGroupHelper.PartialView, AnchorButtonGroupHelper.GetEditDetailsDelete(item.NewsId.ToString()))
                        </td>
                    </tr>
                }`
				
##Back End

The first back end story I worked on was to change CalEvents to CalendarEvents throughout the entire project. This meant going through and changing all references including the database. Through this story I learned how migration works and how our project was set to automigrate. With a simple command of update-database in the NuGet package console the database was usually fixed and code working. I used this a lot in my time on live project. 

`// GET: Calendar
        public ActionResult Index()
        {
            return View();
        }
        public JsonResult GetEvents()
        {
            //CHANGED NAME FROM CALEVENTS TO CALENDAREVENTS
            using (Calendar dc = new Calendar())
            {
                var events = dc.CalendarEvents.ToList();
                return new JsonResult { Data = events, JsonRequestBehavior = JsonRequestBehavior.AllowGet };
            }
        }

        [HttpPost]
        public JsonResult SaveEvent(CalendarEvent e)
        {
            var status = false;
            using (Calendar dc = new Calendar())
            {
                if (e.EventID > 0)
                {
                    //Update the event
                    var v = dc.CalendarEvents.Where(a => a.EventID == e.EventID).FirstOrDefault();
                    if (v != null)
                    {
                        v.Subject = e.Subject;
                        v.Start = e.Start;
                        v.End = e.End;
                        v.Description = e.Description;
			}
			
`I learned about SignalR and how to create and work with a Chat modal. In our project the chat modal was already created but was not functioning so I reviews the code and analyzed how SignalR works and realized at the same time as the project managed that the story was unnecessary as the reason it was not working it because we have to have username to use the modal. So I worked on implementing a user into the Seed data. This gave the database information so that the team could test the site with the created users. 


`//HARD CODE USER
        private void seedUsers(ApplicationDbContext context)       
        {
            var UserManager = new UserManager<ApplicationUser>(new UserStore<ApplicationUser>(context));
            if (UserManager.FindByNameAsync("user1").Result == null)
            {
                ApplicationUser user = new ApplicationUser();
                user.UserName = "user1";
                user.Email = "manager@gmail.com";
                user.FirstName = "Adam";
                user.LastName = "Inistrator";
                user.WorkType = Enums.WorkType.Foreman;
                user.UserRole = "Manager";
                user.Suspended = false;
                //Calls UserManager to assign role
                IdentityResult result = UserManager.Create(user, "P@ssw0rd");
                if (result.Succeeded)
                {
                    UserManager.AddToRole(user.Id, "Manager");
                }
            }`

Another story that was small but taught me a lot was fixing that display name so that is used the users First name and their last inital. This was a challenge for me because I did not know anything of how to use {get; set;} but I learned and while struggling had to ask the instructor for help but figured it out before he could. I was a huge ego boost to know I can figure it out if I just play with it long enough.

`public class ApplicationUser : IdentityUser {
        //SET DISPLAYNAME WITH FIRST NAME AND LAST INITIAL
        public string DisplayName { get { return FirstName + " " + LastName.Substring(0, 1); } internal set { FirstName = value; LastName = value; } }`

Although I felt like I was doing a little of everything everywhere I realized in the end that I did much of my front end work on the Company News and I also did this back end story in that area. I removed that user input of the date the news was created to have this property be auto implemented when the user submits the news entry.

`if (ModelState.IsValid)
            {
                //AUTOMATICALLY ADD DATE STAMP
                companyNews.DateStamp = DateTime.Now.ToString("MM / dd / yyyy");
                db.CompanyNews.Add(companyNews);
                db.SaveChanges();
                return RedirectToAction("Index");
            }

            return View(companyNews);
			
@Html.ValidationSummary(true, "", new { @class = "text-danger" })
        @*<div class="form-group">
		REMOVED DATE PICKER FOR AUTOMATIC DateStamp
            @Html.LabelFor(model => model.DateStamp, htmlAttributes: new { @class = "control-label col-md-2" })
            <div class="col-md-10">
                @Html.EditorFor(model => model.DateStamp, new { htmlAttributes = new { @class = "form-control datepicker", required = "required" } })
                @Html.ValidationMessageFor(model => model.DateStamp, "", new { @class = "text-danger" })
            </div>
        </div>*@`
			
##Other 

From the very first day on Live project I was finding bugs or issues to resolve. On my first day I could not assess the project because it was written in Visual Studio 2017 and I was using the newer version 2019. They bosses believed it would be ok but found out that was not the case and after spending the whole day with everyone looking as the code to see why it would not work they finally just had me install the older version of VS. Then I stumble into another issue with the above code (changing calEvents) that revealed a need to restructure the whole database. And lastly I when I worked on the auto date of company news I worked around the bug when the buttons were not working if you logged in and exposed this to the project manager who thought originally it was just my branch, later we found it was a bigger bug that the whole team was having issues with however the new members were not communicating well. I was able to find and fix the small button issue but it exposed more troubles and hopefully helped to get the team talking more. 
Here is the fix I made.

`<div class="form-group">
            @*ADDED "CREATEBTN" TO FIX BUG*@
            <div class="col-md-offset-2 col-md-10 createBtn">
                <input type="submit" value="Create" class="btn btn-default" />
            </div>
        </div>`
