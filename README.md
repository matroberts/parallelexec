# Parallel Exec

Parallel Test Run
=================
Idea: Run one set of tests per core of the build server, and therefore have one database per core of the build server.
- Basically have a list of m jobs you want to do, and n concurrent exe's running to do it (where m >> n)
- Need to pass per process environment variable to the exe's so can control which database is used

Exe or msbuild task
===================
Think msbuild task, cos this is what were gonna call it from

ToDo
====
msbuild custom task

Create process
- need to capture stdout, stderr and redirect to 
- need to make sure stop on error.....but the parallel should move onto the next one
  - if an error happens in any parallel need to return non-zero exit code



How detect number of cores
https://stackoverflow.com/questions/1542213/how-to-find-the-number-of-cpu-cores-via-net-c


How pass environment variable to db connection on test run?
   See TestBaseDb Configuration got from AppSettings, just need to add the code that lets you override with an environment variable. 
   https://stackoverflow.com/a/14582921/791058

How parallize db build?
???

How kick of process, with parallel malarky
* parallel foreach, with MaxDefreeOfParallelism would allow you to work though a list of things, doing them 4 at a time say
* The thing you need to do is asyncronously create a process and wait for it to finish
    https://stackoverflow.com/a/10789196/791058
	Need to at least capture the error stream, cos need to be able to see errors when it cocks up (both streams really

Parallel.Foreach, with MaxDefreeOfParallelism

OR With SemaphoreSlim

var sem = new SemaphoreSlim(4);
var data = new List<int>();
var tasks = new List<Task>();
foreach (var item in data)
{
    var t = Task.Run(async () =>
    {
    await sem.WaitAsync();
    try
    {
        //Run and wait
    }
    finally
    {
         sem.Release();
    }

});
tasks.Add(t);
}

Task.WhenAll(tasks);