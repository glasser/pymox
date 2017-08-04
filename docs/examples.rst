Example 1

From project python-keystoneclient, under directory tests, in source file utils.py.

Score: 16
 vote
 vote
def setUp(self):
        super(TestCase, self).setUp()
        self.mox = mox.Mox()
        self._original_time = time.time
        time.time = lambda: 1234
        httplib2.Http.request = self.mox.CreateMockAnything()
        self.client = client.Client(username=self.TEST_USER,
                                    token=self.TEST_TOKEN,
                                    project_id=self.TEST_TENANT,
                                    auth_url=self.TEST_URL,
                                    endpoint=self.TEST_URL)

     

 
Example 2

From project hyou-master, under directory test, in source file util_test.py.

Score: 10
 vote
 vote
def setUp(self):
    self.mox = mox.Mox()
    self.enumerator = self.mox.CreateMockAnything()
    self.constructor = self.mox.CreateMockAnything()
    self.dict = hyou.util.LazyOrderedDictionary(
        enumerator=self.enumerator, constructor=self.constructor)

   
Example 3

From project hyou-master, under directory test, in source file spreadsheet_test.py.

Score: 10
 vote
 vote
def setUp(self):
    self.mox = mox.Mox()
    self.mox.StubOutClassWithMocks(hyou.client, 'Worksheet')
    self.client = self.mox.CreateMock(
        gdata.spreadsheets.client.SpreadsheetsClient)
    self.drive = self.mox.CreateMockAnything()
    entry = FakeSpreadsheetFeed('Cinamon')
    self.spreadsheet = hyou.client.Spreadsheet(
        None, self.client, self.drive, 'cinamon', entry)

   

 
Example 4

From project hyou-master, under directory test, in source file worksheet_test.py.

Score: 10
 vote
 vote
def setUp(self):
    self.mox = mox.Mox()
    self.client = self.mox.CreateMock(
        gdata.spreadsheets.client.SpreadsheetsClient)
    self.worksheet = hyou.client.Worksheet(
        FakeSpreadsheet('cinamon'),
        self.client,
        's1',
        FakeWorksheetFeed('Sheet1', '2', '5'))

   
Example 5

From project akanda-rug-master, under directory akanda/rug/test/unit/openvswitch, in source file test_ovs_lib.py.

Score: 10
 vote
 vote
def setUp(self):
        super(OVS_Lib_Test, self).setUp()
        self.BR_NAME = "br-int"
        self.TO = "--timeout=2"

        self.mox = mox.Mox()
        self.root_helper = 'sudo'
        self.br = ovs_lib.OVSBridge(self.BR_NAME, self.root_helper)
        self.mox.StubOutWithMock(utils, "execute")
        self.addCleanup(self.mox.UnsetStubs)

     
Example 6

From project interstellar-master, under directory gsutil/third_party/gcs-oauth2-boto-plugin/gcs_oauth2_boto_plugin, in source file test_oauth2_client.py.

Score: 10
 vote
 vote
def setUp(self):
    self.mox = mox.Mox()
    self.mox.StubOutClassWithMocks(httplib2, 'Http')
    self.mock_http = httplib2.Http()

   
Example 7

From project django-lean, under directory django_lean/experiments/tests, in source file test_tags.py.

Score: 10
 vote
 vote
def setUp(self):
        self.experiment = Experiment(name="test_experiment")
        self.experiment.save()
        self.experiment.state = Experiment.ENABLED_STATE
        self.experiment.save()
        
        self.other_experiment = Experiment(name="other_test_experiment")
        self.other_experiment.save()
        self.other_experiment.state = Experiment.ENABLED_STATE
        self.other_experiment.save()
        self.mox = mox.Mox()
    
     
Example 8

From project django-lean, under directory django_lean/experiments/tests, in source file test_daily_report.py.

Score: 10
 vote
 vote
def testZeroParticipantExperiment(self):
        mocker = mox.Mox()
        engagement_calculator = mocker.CreateMockAnything()
        mocker.ReplayAll()
        
        report_date = date.today()
        EngagementReportGenerator(engagement_score_calculator=engagement_calculator).generate_daily_report_for_experiment(
            self.other_experiment, report_date)
        
        experiment_report = DailyEngagementReport.objects.get(
            experiment=self.other_experiment, date=report_date)
        
        mocker.VerifyAll()
        
        self.assertEquals(None, experiment_report.test_score)
        self.assertEquals(None, experiment_report.control_score)
        self.assertEquals(0, experiment_report.test_group_size)
        self.assertEquals(0, experiment_report.control_group_size)
    
     
Example 9

From project django-lean, under directory django_lean/lean_retention/tests, in source file test_reports.py.

Score: 10
 vote
 vote
def setUp(self):
        self.mox = mox.Mox()
        self.user = User.objects.create_user('user', 'user@example.com', 'user')
        self.activity, _ = DailyActivity.objects.stamp(user=self.user,
                                                       site=get_current_site(),
                                                       medium='Default')
        self.activity.days = 29
        self.activity.save()

     
Example 10

From project protobuf-objc, under directory python/google/protobuf/internal, in source file decoder_test.py.

Score: 10
 vote
 vote
def setUp(self):
    self.mox = mox.Mox()
    self.mock_stream = self.mox.CreateMock(input_stream.InputStream)
    self.mock_message = self.mox.CreateMock(message.Message)

   
Example 11

From project protobuf-objc, under directory python/google/protobuf/internal, in source file encoder_test.py.

Score: 10
 vote
 vote
def setUp(self):
    self.mox = mox.Mox()
    self.encoder = encoder.Encoder()
    self.mock_stream = self.mox.CreateMock(output_stream.OutputStream)
    self.mock_message = self.mox.CreateMock(message.Message)
    self.encoder._stream = self.mock_stream

   
Example 12

From project Godel, under directory src/Godel/tests, in source file test_rule_engine.py.

Score: 10
 vote
 vote
def __init__(self):
        self.tag_stack = []
        self.state_stack = []
        self.stack = []
        self.hypergraph = None
        self.Groundings = {}
        self.Types = {}
        
        self.mox = mox.Mox()
        self.hypergraph = self.mox.CreateMockAnything()
        self.hypergraph.AnyMethod()
        
        
     
Example 13

From project cc, under directory nova, in source file test.py.

Score: 10
 vote
 vote
def setUp(self):
        super(TrialTestCase, self).setUp()

        # emulate some of the mox stuff, we can't use the metaclass
        # because it screws with our generators
        self.mox = mox.Mox()
        self.stubs = stubout.StubOutForTesting()
        self.flag_overrides = {}

     
Example 14

From project imagr-master, under directory Imagr/gmacpyutil, in source file timer_test.py.

Score: 10
 vote
 vote
def setUp(self):
    self.mox = mox.Mox()
    self.mox.StubOutWithMock(timer.gmacpyutil, 'SetPlistKey')
    self.mox.StubOutWithMock(timer.gmacpyutil, 'GetPlistKey')

    self.timeplist = '/tmp/blah/myapp.plist'
    self.interval = datetime.timedelta(hours=23)
    self.tf = timer.TimeFile(self.timeplist)

   
Example 15

From project imagr-master, under directory Imagr/gmacpyutil, in source file ds_test.py.

Score: 10
 vote
 vote
def setUp(self):
    self.mox = mox.Mox()
    self.mox.StubOutWithMock(ds.gmacpyutil, 'RunProcess')
    if os.uname()[0] == 'Linux':
      self.InitMockFoundation()
    elif os.uname()[0] == 'Darwin':
      self.StubFoundation()

   
Example 16

From project google-apputils-master, under directory tests, in source file file_util_test.py.

Score: 10
 vote
 vote
def setUp(self):
    self.mox = mox.Mox()
    self.sample_contents = 'Contents of the file'
    self.file_path = '/path/to/some/file'
    self.fd = 'a file descriptor'

   
Example 17

From project Android-Development, under directory tools/scripts, in source file divide_and_compress_test.py.

Score: 7
 vote
 vote
def setUp(self):
    """Prepare the test.

    Construct some mock objects for use with the tests.
    """
    self.my_mox = mox.Mox()
    file1 = BagOfParts()
    file1.filename = 'file1.txt'
    file1.contents = 'This is a test file'
    file2 = BagOfParts()
    file2.filename = 'file2.txt'
    file2.contents = ('akdjfk;djsf;kljdslkfjslkdfjlsfjkdvn;kn;2389rtu4i'
                      'tn;ghf8:89H*hp748FJw80fu9WJFpwf39pujens;fihkhjfk'
                      'sdjfljkgsc n;iself')
    self.files = {'file1': file1, 'file2': file2}

   
Example 18

From project nova, under directory nova/tests/xenapi, in source file test_vm_utils.py.

Score: 7
 vote
 vote
def test_lookup_call(self):
        mock = mox.Mox()
        mock.StubOutWithMock(vm_utils, 'lookup')

        vm_utils.lookup('session', 'somename').AndReturn('ignored')

        mock.ReplayAll()
        vm_utils.vm_ref_or_raise('session', 'somename')
        mock.VerifyAll()

     
Example 19

From project nova, under directory nova/tests/virt/xenapi/imageupload, in source file test_glance.py.

Score: 7
 vote
 vote
def setUp(self):
        super(TestGlanceStore, self).setUp()
        self.store = glance.GlanceStore()
        self.mox = mox.Mox()

     
Example 20

From project nova, under directory nova, in source file test.py.

Score: 7
 vote
 vote
def setUp(self):
        super(MoxStubout, self).setUp()
        # emulate some of the mox stuff, we can't use the metaclass
        # because it screws with our generators
        self.mox = mox.Mox()
        self.stubs = stubout.StubOutForTesting()
        self.addCleanup(self.mox.UnsetStubs)
        self.addCleanup(self.stubs.UnsetAll)
        self.addCleanup(self.stubs.SmartUnsetAll)
        self.addCleanup(self.mox.VerifyAll)


 
Example 21

From project nappingcat, under directory tests/gittests, in source file utils.py.

Score: 7
 vote
 vote
def test_uses_getlogin(self):
        settings = {
            'host':'host-%d' % random.randint(1,100),
        }
        _mox = mox.Mox()
        _mox.StubOutWithMock(os, 'getlogin')
        random_user = 'rand-%d' % random.randint(1,100)
        os.getlogin().AndReturn(random_user)
        _mox.ReplayAll()
        results = utils.get_clone_base_url(settings)
        self.assertEqual('%s@%s' % (random_user, settings['host']), results)
        _mox.UnsetStubs()

     
Example 22

From project appengine-python3-master, under directory google/appengine/tools/devappserver2/admin, in source file datastore_viewer_test.py.

Score: 7
 vote
 vote
def setUp(self):
    self.app_id = 'myapp'
    os.environ['APPLICATION_ID'] = self.app_id
    api_server.test_setup_stubs(app_id=self.app_id)

    self.mox = mox.Mox()
    self.mox.StubOutWithMock(admin_request_handler.AdminRequestHandler,
                             'render')

   
Example 23

From project appengine-python3-master, under directory google/appengine/tools/devappserver2/admin, in source file taskqueue_queues_handler_test.py.

Score: 7
 vote
 vote
def setUp(self):
    self.mox = mox.Mox()
    self.mox.StubOutWithMock(taskqueue_utils.QueueInfo, 'get')
    self.mox.StubOutWithMock(admin_request_handler.AdminRequestHandler,
                             'render')

   
Example 24

From project habitat, under directory habitat/tests/test_utils, in source file test_startup.py.

Score: 7
 vote
 vote
def setup(self):
        self.mocker = mox.Mox()
        self.config = copy.deepcopy(_logging_config)

        self.old_handlers = logging.root.handlers
        # nose creates its own handler
        logging.root.handlers = []

        # manual cleanup needed for check_file's tests
        self.temp_dir = None
        self.temp_files = []

     
Example 25

From project habitat, under directory habitat/tests/test_utils, in source file test_immortal_changes.py.

Score: 7
 vote
 vote
def setup(self):
        self.m = mox.Mox()

        self.consumer = immortal_changes.Consumer(None,
            backend='habitat.tests.test_utils.'
                    'test_immortal_changes.DummyConsumer')
        assert isinstance(self.consumer._consumer, DummyConsumer)
        self.m.StubOutWithMock(self.consumer._consumer, "wait")

        assert immortal_changes.time == time
        immortal_changes.time = DummyTimeModule()
        self.m.StubOutWithMock(immortal_changes.time, "sleep")

        self.m.StubOutWithMock(immortal_changes.logger, "exception")

        # for brevity.
        self.backend = self.consumer._consumer.wait
        self.sleep = immortal_changes.time.sleep
        self.exc = immortal_changes.logger.exception
        self.cb = self.m.CreateMockAnything()

     
Example 26

From project habitat, under directory habitat/tests, in source file test_parser_daemon.py.

Score: 7
 vote
 vote
def setup(self):
        self.m = mox.Mox()

        self.config = {
            "couch_uri": "http://localhost:5984", "couch_db": "test"}

        self.m.StubOutWithMock(parser_daemon, 'couchdbkit')
        self.m.StubOutWithMock(parser_daemon, 'immortal_changes')
        self.m.StubOutWithMock(parser_daemon, 'parser')
        self.mock_server = self.m.CreateMock(couchdbkit.Server)
        self.mock_db = self.m.CreateMock(couchdbkit.Database)
        parser_daemon.couchdbkit.Server("http://localhost:5984")\
                .AndReturn(self.mock_server)
        self.mock_server.__getitem__("test").AndReturn(self.mock_db)
        self.mock_db.info().AndReturn({"update_seq": 191238})
        parser_daemon.parser.Parser(self.config)

        self.m.ReplayAll()
        self.daemon = parser_daemon.ParserDaemon(self.config)
        self.m.VerifyAll()
        self.m.ResetAll()

     
Example 27

From project habitat, under directory habitat/tests/test_parser, in source file test_parser.py.

Score: 7
 vote
 vote
def setup(self):
        self.m = mox.Mox()
        self.mock_module = self.m.CreateMock(parser.ParserModule)

        class MockModule(parser.ParserModule):
            def __new__(cls, parser):
                return self.mock_module

        base_path = os.path.split(os.path.abspath(__file__))[0]
        cert_path = os.path.join(base_path, 'certs')
        self.parser_config = {"parser": {"modules": [
            {"name": "Mock", "class": MockModule}],
            "certs_dir": cert_path}, "loadables": [],
            "couch_uri": "http://localhost:5984", "couch_db": "test"}

        self.m.StubOutWithMock(parser, 'couchdbkit')
        self.mock_server = self.m.CreateMock(couchdbkit.Server)
        self.mock_db = self.m.CreateMock(couchdbkit.Database)
        parser.couchdbkit.Server("http://localhost:5984")\
                .AndReturn(self.mock_server)
        self.mock_server.__getitem__("test").AndReturn(self.mock_db)

        self.m.ReplayAll()
        self.parser = parser.Parser(self.parser_config)
        self.m.VerifyAll()
        self.m.ResetAll()

     
Example 28

From project WMCore, under directory test/python/WMCore_t/Alerts_t/ZMQ_t/Sinks_t, in source file EmailSink_t.py.

Score: 7
 vote
 vote
def setUp(self):
        self.config = ConfigSection("email")
        self.config.fromAddr = "some@local.com"
        self.config.toAddr = ["some1@local.com", "some2@local.com"]
        self.config.smtpServer = "smtp.gov"
        self.config.smtpUser = None
        self.config.smtpPass = None

        # now we want to mock smtp emailing stuff - via pymox - no actual
        # email sending to happen
        self.mox = mox.Mox()
        self.smtpReal = EmailSinkMod.smtplib
        EmailSinkMod.smtplib = self.mox.CreateMock(EmailSinkMod.smtplib)
        self.smtp = self.mox.CreateMockAnything()


     
Example 29

From project WMCore, under directory test/python/WMCore_t/Storage_t/Plugins_t, in source file SRMV2Impl_t.py.

Score: 7
 vote
 vote
def setUp(self):
        self.my_mox = mox.Mox()
        self.my_mox.StubOutWithMock(moduleWeAreTesting.os.path, 'getsize')
        self.my_mox.StubOutWithMock(moduleWeAreTesting,'runCommand')
        self.my_mox.StubOutWithMock(moduleWeAreTesting,'tempfile')
        self.popenMocker = self.my_mox.CreateMock(popenMockHelper)
        self.popenBackup = moduleWeAreTesting.Popen


        self.temporaryFiles = []
        self.rules          = []
     
Example 30

From project panfletario, under directory r2/r2/tests/unit, in source file test_link.py.

Score: 7
 vote
 vote
def test_make_permalink(self):
        m  = mox.Mox()
        subreddit = m.CreateMock(self.models.Subreddit)
        #subreddit.name.AndReturn('stuff')
        m.ReplayAll()

        pylons.c.default_sr = True #False
        pylons.c.cname = False
        link = self.models.Link(name = 'Link Name', url = 'self', title = 'A link title', sr_id = 1)
        link._commit()
        permalink = link.make_permalink(subreddit)

        m.VerifyAll()
        assert permalink == '/lw/%s/a_link_title/' % link._id36


    # def test_make_permalink_slow(self):
    #
    #
    #     link = self.models.Link(name = 'Link Name', url = 'self', sr_id = 1)
    #     m = mox.Mox()
    #     mock_subreddit = mox.MockObject(self.models.Subreddit)
    #
    #     m.StubOutWithMock(link, 'subreddit_slow', use_mock_anything=True)
    #     link.subreddit_slow().AndReturn(mock_subreddit)
    #
    #     m.ReplayAll()
    #
    #     permalink = link.make_permalink_slow()
    #
    #     m.UnsetStubs()
    #     m.VerifyAll()


     
Example 31

From project hyou-master, under directory test, in source file collection_test.py.

Score: 7
 vote
 vote
def setUp(self):
    self.mox = mox.Mox()
    self.mox.StubOutClassWithMocks(hyou.client, 'Spreadsheet')
    self.client = self.mox.CreateMock(
        gdata.spreadsheets.client.SpreadsheetsClient)
    self.drive = self.mox.CreateMockAnything()
    self.collection = hyou.client.Collection(self.client, self.drive)

   
Example 32

From project python-typepad-api, under directory tests, in source file test_tpobject.py.

Score: 5
 vote
 vote
def test_responseless(self):
        request = {
            'uri': mox.Func(self.saver('uri')),
            'method': 'POST',
            'headers': mox.Func(self.saver('headers')),
            'body': mox.Func(self.saver('body')),
        }
        response = {
            'status': 204,  # no content
        }

        http = typepad.TypePadClient()
        typepad.client = http
        http.add_credentials(
            OAuthConsumer('consumertoken', 'consumersecret'),
            OAuthToken('tokentoken', 'tokensecret'),
            domain='api.typepad.com',
        )

        mock = mox.Mox()
        mock.StubOutWithMock(http, 'request')
        http.request(**request).AndReturn((httplib2.Response(response), ''))
        mock.ReplayAll()

        class Moose(typepad.TypePadObject):

            class Snert(typepad.TypePadObject):
                volume = typepad.fields.Field()
            snert = typepad.fields.ActionEndpoint(api_name='snert', post_type=Snert)

        moose = Moose()
        moose._location = 'https://api.typepad.com/meese/7.json'

        ret = moose.snert(volume=10)
        self.assert_(ret is None)

        mock.VerifyAll()

        self.assert_(self.uri)
        self.assertEquals(self.uri, 'https://api.typepad.com/meese/7/snert.json')
        self.assert_(self.headers)
        self.assert_(self.body)

        self.assert_(utils.json_equals({
            'volume': 10
        }, self.body))

     
Example 33

From project horizon, under directory horizon/horizon, in source file test.py.

Score: 4
 vote
 vote
def setUp(self):
        self.mox = mox.Mox()

        def fake_conn_request(*args, **kwargs):
            raise Exception("An external URI request tried to escape through "
                            "an httplib2 client. Args: %s, kwargs: %s"
                            % (args, kwargs))
        self._real_conn_request = httplib2.Http._conn_request
        httplib2.Http._conn_request = fake_conn_request

        self._real_horizon_context_processor = context_processors.horizon
        context_processors.horizon = lambda request: self.TEST_CONTEXT

        self._real_get_user_from_request = users.get_user_from_request
        self.setActiveUser(token=self.TEST_TOKEN,
                           username=self.TEST_USER,
                           tenant_id=self.TEST_TENANT,
                           service_catalog=self.TEST_SERVICE_CATALOG)
        self.request = http.HttpRequest()
        middleware.HorizonMiddleware().process_request(self.request)

     
Example 34

From project nova, under directory nova/tests, in source file test_hypervapi.py.

Score: 2
 vote
 vote
def __init__(self, test_case_name):
        self._mox = mox.Mox()
        super(HyperVAPITestCase, self).__init__(test_case_name)

     
Example 35

From project nappingcat, under directory tests/gittests, in source file handlers.py.

Score: 2
 vote
 vote
def setUp(self):
        self.test_dir = os.path.expanduser('~/.kittygittests')
        self.mox = mox.Mox()

     
Example 36

From project nappingcat, under directory tests/gittests, in source file operations.py.

Score: 2
 vote
 vote
def setUp(self):
        self.mox = mox.Mox()
        self.cleanup_dirs = []

     
Example 37

From project nappingcat, under directory tests, in source file config.py.

Score: 2
 vote
 vote
def setUp(self):
        self.mox = mox.Mox()
        self.filename = 'tests/.%d.conf' % random.randint(1,100)

     
Example 38

From project nappingcat, under directory tests, in source file serve.py.

Score: 2
 vote
 vote
def setUp(self):
        self.mox = mox.Mox()

     
Example 39

From project appengine-python3-master, under directory google/appengine/tools/devappserver2/admin, in source file taskqueue_utils_test.py.

Score: 2
 vote
 vote
def setUp(self):
    self.mox = mox.Mox()
    self.mox.StubOutWithMock(apiproxy_stub_map, 'MakeSyncCall')

   
Example 40

From project appengine-python3-master, under directory google/appengine/tools/devappserver2/admin, in source file xmpp_request_handler_test.py.

Score: 2
 vote
 vote
def setUp(self):
    self.mox = mox.Mox()

   
Example 41

From project habitat, under directory habitat/tests/test_loadable_manager, in source file test_loadable_manager.py.

Score: 2
 vote
 vote
def setup(self):
        self.mocker = mox.Mox()
        self.mocker.StubOutWithMock(loadable_manager, "dynamicloader")

     
Example 42

From project analyzer-master, under directory tests/unit, in source file broken_tick_feeder.py.

Score: 2
 vote
 vote
def setUp(self):
        self.mock = mox.Mox()

     
Example 43

From project django-lean, under directory django_lean/lean_analytics, in source file tests.py.

Score: 2
 vote
 vote
def setUp(self):
            self.mox = mox.Mox()
            self.analytics = KissMetrics()

         
Example 44

From project cc, under directory vendor/pymox, in source file stubout_test.py.

Score: 2
 vote
 vote
def setUp(self):
    self.mox = mox.Mox()
    self.sample_function_backup = stubout_testee.SampleFunction
