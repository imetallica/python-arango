Async Execution
---------------

Python-arango provides support for **asynchronous execution**, where incoming
requests are placed in a server-side, in-memory task queue and executed in a
fire-and-forget style. The results of the requests can be retrieved later via
:ref:`AsyncJob` objects.

For more information on the HTTP REST API for retrieving asynchronous results
visit this `page <https://docs.arangodb.com/HTTP/AsyncResultsManagement>`_.

**Example:**

.. code-block:: python

    from arango import ArangoClient

    client = ArangoClient()
    db = client.db('my_database')

    # Initialize an AsyncExecution object to make asynchronous calls
    async = db.async(return_result=True)

    # AsyncExecution has a similar interface as that of Database, but
    # AsyncJob objects are returned instead of results on API calls
    job1 = async.collection('students').insert({'_key': 'Abby'})
    job2 = async.collection('students').insert({'_key': 'John'})
    job3 = async.collection('students').insert({'_key': 'John'})
    job4 = async.aql.execute('FOR d IN students RETURN d')

    # Once the jobs finish, their results can be accessed before expiry
    for job in [job1, job2, job3, job4]:
        print(job, job.status())

    # Retrieve the result of a job
    job1.result()

    # If a job fails the error is returned as opposed to being raised
    assert isinstance(job3.result(), Exception)

    # Cancel a pending job
    job3.cancel()

    # Clear a result of a job from the server
    job4.clear()


Refer to the :ref:`AsyncExecution` and :ref:`AsyncJob` classes for more
details.