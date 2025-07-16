# SeniorEngineer_Opinov8

2 - Legacy Platform Review & Proposed Enhancement

 Some technical limitations of a monolithic architecture are:
    - Hard to scale parts independently making it a less than flexible environment and less future prone in case there's a need to add/decouple features
    - It's harder to maintain in the sense that the frontend and backend are tightly integrated which means a change in one will require changes to the other. This can be time consuming and costly.

Technical enhancement
    - One could be a security uplift, where we could create a different authentication feature. Usually monoliths use a session based authentication which is tightly coupled with the app. Creating an auth based API using JWT for example, allows it the app to call it instead of authenticating directly. The design of this API implements secure token standards, expiration and scopes and allows to use it in a different and more modern architecture like API-first or microservices.

3 - Future-State Architecture 
    I would recommend a API-FIrst approach for a new solution architecture.
        -  The advantages of it is that the application could use diferent services throughout, allowing for more flexibility in what to add/decouple from the app. This provides better maintainability, flexibility and enables for a more agile approach amking it quicker to adapt to new changes in the market
        - CI/CD to keep track of changes, allowing for quick fixes and more frequent releases.
        - Observability to monitor performance, errors and incidents that occur. This provides a better response to problems that may arise and diagnose performance issues
4- Hands-On example

    Using a method I created recently using an inline SQL statement we could refactor it into a Repository that allows us to move it into an API-FIrst approach.

        public static List<UserCourseProgress> GetUserCourseProgress()
        {
            // Query to retrieve user course progress with course details
            var getAllData = "SELECT ucp.Id AS UserCourseProgressId, ucp.UserId, ucp.CourseId, ucp.IsCompleted, icd.Title, icd.Thumbnail, icd.Users FROM UserCourseProgress ucp LEFT JOIN ItemType_CourseDetail icd ON ucp.CourseId = icd.Id";

            List<UserCourseProgress> result = new List<UserCourseProgress>();
            
            using (IDataReader getUserProgressData = Database.CreateDataReader(getAllData))
            {
                while (getUserProgressData.Read())
                {
                    result.Add(new UserCourseProgress
                    {
                        CourseId = getUserProgressData["CourseId"].ToString(),
                        UserId = (int)getUserProgressData["UserId"],
                        IsCompleted = (bool)getUserProgressData["IsCompleted"],
                        Title = getUserProgressData["Title"] == DBNull.Value ? null : getUserProgressData["Title"].ToString(),
                        Thumbnail = getUserProgressData["Thumbnail"] == DBNull.Value ? null : getUserProgressData["Thumbnail"].ToString(),
                        Users = getUserProgressData["Users"] == DBNull.Value || string.IsNullOrEmpty(getUserProgressData["Users"].ToString())
                            ? new List<string>()
                            : getUserProgressData["Users"].ToString().Split(',').ToList(),

                    });
                }
            }

            return result;
        }

    The UserCourseProgress already exists which is copied below:

        public class UserCourseProgress
            {
                public int UserId { get; set; }
                public string CourseId { get; set; }
                public bool IsCompleted { get; set; }
                public string Title { get; set; }
                public string Thumbnail { get; set; }
                public List<string> Users { get; set; }
            }

    We could then create a repository interface:

        public interface IUserCourseProgressRepository
            {
                List<UserCourseProgress> GetAllUserCourseProgress();
            }

    Implement the Interface method to isolate the SQL:

        public class UserCourseProgressRepository : IUserCourseProgressRepository
            {
                public List<UserCourseProgress> GetAllUserCourseProgress()
                {
                    var query = @"
                        SELECT 
                            ucp.Id AS UserCourseProgressId, 
                            ucp.UserId, 
                            ucp.CourseId, 
                            ucp.IsCompleted, 
                            icd.Title, 
                            icd.Thumbnail, 
                            icd.Users 
                        FROM UserCourseProgress ucp 
                        LEFT JOIN ItemType_CourseDetail icd ON ucp.CourseId = icd.Id";

                    var result = new List<UserCourseProgress>();

                    using (IDataReader reader = Database.CreateDataReader(query))
                    {
                        while (reader.Read())
                        {
                            result.Add(new UserCourseProgress
                            {
                                CourseId = reader["CourseId"].ToString(),
                                UserId = (int)reader["UserId"],
                                IsCompleted = (bool)reader["IsCompleted"],
                                Title = reader["Title"] == DBNull.Value ? null : reader["Title"].ToString(),
                                Thumbnail = reader["Thumbnail"] == DBNull.Value ? null : reader["Thumbnail"].ToString(),
                                Users = reader["Users"] == DBNull.Value || string.IsNullOrEmpty(reader["Users"].ToString())
                                    ? new List<string>()
                                    : reader["Users"].ToString().Split(',').ToList()
                            });
                        }
                    }

                    return result;
                }
            }

    Inject it into a controller so we can expose the logic via OpenAPI (for our API-First based approach)

        public class UserProgressController : ControllerBase
            {
                private readonly IUserCourseProgressService _service;

                public UserProgressController(IUserCourseProgressService service)
                {
                    _service = service;
                }

                [HttpGet]
                public IActionResult GetAll()
                {
                    var progress = _service.GetProgressForAllUsers();
                    return Ok(progress);
                }
            }


5 - Documentation
    - My final thoughts is that an application should be future-proof and flexible. As I dive into my past experience implementing good code practices which includes DRY, clean code that allows for better maintanability and for extending eventual future changes, these were mostly done in monolithic websites where development time was/is sometimes longer and not very flexible to integrate new services.
    - I believe modern applications should be lego-layed so we can add or remove services as we wish or as the client wishes, in summary make it agile so we can decrease development time, cut costs, decrease time to market features and deployments.


    