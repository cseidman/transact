# Transaction Processor

###### Claude Seidman : claude@intellixus.com





| Category              | Comments                                                     |
| --------------------- | ------------------------------------------------------------ |
| Basics                | The application builds, reads data properly, and is properly formatted according to specification. |
| Completeness          | I believe I handle all reasonably imaginable case such as: deposits, withdrawals, disputes, resolutions, and charge-backs.  I also prevent attempts at generating negative balances. I handle possible system level mistakes such as withdrawals and disputes against non-existent accounts as well as resolutions or charge-backs against transactions where there is no dispute. We ignore all actions against locked accounts.<br />I did take the liberty of assuming that clients only dispute withdrawals and not deposits. I ignore disputes against anything else assuming it's a mistake. Aside from the common sense reasons for this, there are so many business implications  around disputing deposits that I thought surely it wouldn't be part of this task. |
| Correctness           | I am using unit tests to test the scenarios I describe above. These are small samples where I can aggregate numbers and use them in test assertions to be sure the balances, statuses, and counts are correct. |
| Safety and Robustness | I confess that in the interest of time, I relied far more on **panic!** and **.expect** than I would ever do in a real production application. In this program, I used them in places where the application would have to halt anyway. In cases where the error is not fatal, I issued warnings to **stderr**. Again, if I had more time, I would have put in proper logging, tracing, and error handling to provide more detail and recoverability when possible. <br /><br />I also assumed the incoming file formats are always correct and fit their described specification. Obviously, in production, I'd want a quick format check in the beginning of the application before it performs heavier processing. |
| Efficiency            | I had a tough time deciding on the approach to take. Being a data-centric application, I naturally gravitate towards using a database engine to support tasks like these. I also know that maybe you would prefer that I code *everything* in Rust to evaluate my skills in the language. <br /><br />In the end, I opted to use a SQLite library to help in large part because this is actually what I would advocate in production for an application like this one. While memory is fast when the number of records are not too large (100k more or less), the speed drops very quickly as we use more data. If we were to ever get near to the billions of rows suggested by the u32 transaction_id , in-memory arrays, vectors, hashes, etc. would be far too slow and would require tfar too much memory to be viable. <br /><br />By using a database, we can take advantage of indexes and optimized data structures to manages the data for us at very reasonable speeds even with billions of rows. In addition, the database engine is able to manage concurrent access to data tables in a way that it could load files from multiple connections at once. |
| Maintainability       | I tried to decompose logic into specialized functions which are easy to follow and I tried to use meaningful names for objects as much as I could. I'm sure that I could take a few more passes over the code and find ways to still improve these things, of course. |

