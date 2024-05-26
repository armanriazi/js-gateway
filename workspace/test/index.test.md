//unit test
import { bcryptTest } from './unit_test/helper/bcrypt.test.js'
import { authTest } from './unit_test/services/auth.test.js'
import { ajvTest } from './unit_test/middleware/ajvValidator.test.js'
import { brokerTest } from './unit_test/services/broker.test.js'
import { busboyTest } from './unit_test/services/busboy.test.js'
import { utilsTest } from './unit_test/services/utils.test.js'
import { paginationTest } from './unit_test/middleware/paginationHandler.test.js'
import { policyTest } from './unit_test/middleware/policy.test.js'
import { axiosTest } from './unit_test/helper/axios.test.js'
import { jwtTest } from './unit_test/helper/jwt.test.js'
import { storeLogTest } from './unit_test/middleware/storeLog.test.js'



//integration test
import { userLogin } from './integration_test/users/login.test'
import { userRegister } from './integration_test/users/createUser.test.js'
import { userUpdate } from './integration_test/users/updateUser.test.js'
import { userPassUpdate } from './integration_test/users/updateUserPass.test.js'
import { userDelete } from './integration_test/users/deleteUser.test.js'
import { userGet } from './integration_test/users/getUser.test.js'
import { userIndex } from './integration_test/users/indexUser.test.js'

import { projectCreate } from './integration_test/projects/createProject.test.js'
import { projectDelete } from './integration_test/projects/deleteProject.test.js'
import { projectGet } from './integration_test/projects/getProject.test.js'
import { projectUpdate } from './integration_test/projects/updateProject.test.js'
import { projectIndex } from './integration_test/projects/indexProject.test.js'

import { serviceCreate } from './integration_test/services/createService.test.js'
import { serviceDelete } from './integration_test/services/deleteService.test.js'
import { serviceGet } from './integration_test/services/getService.test.js'
import { serviceUpdate } from './integration_test/services/updateService.test.js'
import { serviceIndex } from './integration_test/services/serviceIndex.test.js'

import { apiCreate } from './integration_test/apis/createApis.test.js'
import { apiDelete } from './integration_test/apis/deleteApis.test.js'
import { apiGet } from './integration_test/apis/getApis.test.js'
import { apiUpdate } from './integration_test/apis/updateApis.test.js'
import { apiIndex } from './integration_test/apis/indexApi.test.js'

import { apiKeyCreate } from './integration_test/apiKeys/createApiKey.test.js'
import { apiKeyDelete } from './integration_test/apiKeys/deleteApiKey.test.js'
import { apiKeyGet } from './integration_test/apiKeys/getApiKey.test.js'
import { apiKeyUpdate } from './integration_test/apiKeys/updateApiKey.test.js'
import { apiKeyIndex } from './integration_test/apiKeys/indexApiKey.test.js'



describe('integration test the recipes API', () => {
  describe('User:', () => {
    // login user
    userLogin()

    // test register user
    userRegister()

    //update user
    userUpdate()

    //password update
    userPassUpdate()

    //user delete
    userDelete()

    //get user
    userGet()

    //user index
    userIndex()
  })

  describe('Project:', () => {
    //create a project
    projectCreate()

    //delete project
    projectDelete()

    //get project
    projectGet()

    //update project
    projectUpdate()

    // project index
    projectIndex()
  })

  describe('Service:', () => {
    //create service
    serviceCreate()

    //delete service
    serviceDelete()

    //get service
    serviceGet()

    //update service
    serviceUpdate()

    // service index
    serviceIndex()
  })

  describe('Api:', () => {
    //create api
    apiCreate()

    //delete api
    apiDelete()

    //get api
    apiGet()

    //update api
    apiUpdate()

    // service index
    apiIndex()
  })

  describe('ApiKey:', () => {
    //create api
    apiKeyCreate()

    //delete api
    apiKeyDelete()

    //get api
    apiKeyGet()

    //update api
    apiKeyUpdate()

    // apiKey index
    apiKeyIndex()
  })
})


describe('unit testing the methods', () => {

  describe('Bcrypt:', () => {
    bcryptTest()
  })

  describe('Auth:', () => {
    authTest()
  })

  describe('AJV:', () => {
    ajvTest()
  })

  describe('Broker:', () => {
    brokerTest()
  })

  describe('Busboy:', () => {
    // busboyTest()
  })


  describe('Utils:', () => {
    utilsTest()
  })

  describe('Pagination:', () => {
    paginationTest()
  })

  describe('Policy:', () => {
    policyTest()
  })

  describe('Axios:', () => {
    axiosTest()
  })

  describe('JWT:', () => {
    jwtTest()
  })

  describe('storeLog:', () => {
    storeLogTest()
  })
})
