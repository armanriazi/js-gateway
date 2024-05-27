import { server } from '../../../src/app.js'
import supertest from 'supertest'
const request = supertest(server)


import { randomUUID } from 'crypto'

import {
  adminPassword,
  mockApis,
  mockMaxCountTpm,
  mockServiceValidation,
} from '../data/api.js'

import {
  mockApi,
  mockMaxCount,
  mockQueue,
  mockValidation,
  password,
} from '../data/service.js'
import messages from '../../../src/common/messages.js'
import servicesService from '../requiredTest/servicesService.js'
import apiKeyService from '../requiredTest/apiKeyService.js'
import { mockExpireDate, mockUserId } from '../data/apikey.js'
import apiService from '../requiredTest/apiService.js'
import usersService from '../requiredTest/userService.js'
import projectsService from '../requiredTest/projectService.js'

export const apiCreate = () => {
  describe('CREATE API', () => {
    let adminId
    let apiId
    let service
    let token
    let userToken
    let adminToken
    let api
    let serviceId
    let apiKey
    let apiKeyId
    let superAdminId
    let userId
    let projectId

    beforeAll(async () => {
      const superAdminInfo = {
        username: randomUUID(),
        password: adminPassword,
        role: 'superAdmin',
      }

      superAdminId = await usersService.create(superAdminInfo)

      delete superAdminInfo.role

      token = await usersService.login(superAdminInfo)

      const adminInfo = {
        username: randomUUID(),
        password: adminPassword,
        role: 'admin',
      }

      adminId = await usersService.create(adminInfo)

      delete adminInfo.role

      adminToken = await usersService.login(adminInfo)

      const userInfo = {
        username: randomUUID(),
        password: password,
        role: 'user',
      }

      userId = await usersService.create(userInfo)

      delete userInfo.role

      userToken = await usersService.login(userInfo)

      const project = {
        name: randomUUID(),
      }

      projectId = await projectsService.create(project)

      service = {
        apis: mockApi,
        queue: mockQueue,
        validation: mockValidation,
        ProjectId: projectId,
        name: randomUUID(),
      }
      serviceId = await servicesService.create(service)

      apiKey = {
        UserId: userId,
        maxCount: mockMaxCount,
        expireDate: mockExpireDate,
      }

      apiKeyId = await apiKeyService.create(apiKey)
    })

    afterAll(async () => {
      await servicesService.delete(serviceId)
      await apiKeyService.delete(apiKeyId)
      await apiService.delete(apiId)
      await usersService.delete(superAdminId)
      await usersService.delete(userId)
      await usersService.delete(adminId)
      await projectsService.delete(projectId)
    })

    beforeEach(() => {
      api = [
        {
          ApiKeyId: apiKeyId,
          ServiceId: serviceId,
          apis: mockApis,
          TPM: mockMaxCountTpm,
          policies: mockServiceValidation,
          maxCount: 100,
        },
      ]
    })

    it('Registration completed successfully', (done) => {
      try {
        request
          .post('/api/apis')
          .set('Authorization', `Bearer ${token}`)
          .send(api)
          .expect(201)

          .end(function (err, res) {
            if (err) return done(err)

            apiId = res.body.data.message[0].id

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'success',
                message: expect.arrayContaining([
                  expect.objectContaining({
                    id: apiId,
                  }),
                ]),
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('ApiKeyId is required', (done) => {
      try {
        delete api[0].ApiKeyId

        request
          .post('/api/apis')
          .set('Authorization', `Bearer ${token}`)
          .send(api)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.APIKEY_ID} ${messages.m.REQUIRED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('ApiKeyId must be string', (done) => {
      try {
        api[0].ApiKeyId = 876765767

        request
          .post('/api/apis')
          .set('Authorization', `Bearer ${token}`)
          .send(api)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.APIKEY_ID} ${messages.m.INCORRECT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('ApiKeyId should not be empty', (done) => {
      try {
        api[0].ApiKeyId = ''

        request
          .post('/api/apis')
          .set('Authorization', `Bearer ${token}`)
          .send(api)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.APIKEY_ID} ${messages.m.EMPTY}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('ApiKeyId can not be null value', (done) => {
      try {
        api[0].ApiKeyId = null

        request
          .post('/api/apis')
          .set('Authorization', `Bearer ${token}`)
          .send(api)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.APIKEY_ID} ${messages.m.INCORRECT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('ServiceId is required', (done) => {
      try {
        delete api[0].ServiceId
        request
          .post('/api/apis')
          .set('Authorization', `Bearer ${token}`)
          .send(api)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.SERVICE_ID} ${messages.m.REQUIRED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('ServiceId must be string', (done) => {
      try {
        api[0].ServiceId = 876765767

        request
          .post('/api/apis')
          .set('Authorization', `Bearer ${token}`)
          .send(api)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.SERVICE_ID} ${messages.m.INCORRECT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('ServiceId can not be null value', (done) => {
      try {
        api[0].ServiceId = null

        request
          .post('/api/apis')
          .set('Authorization', `Bearer ${token}`)
          .send(api)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.SERVICE_ID} ${messages.m.INCORRECT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('ServiceId should not be empty', (done) => {
      try {
        api[0].ServiceId = ''

        request
          .post('/api/apis')
          .set('Authorization', `Bearer ${token}`)
          .send(api)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.SERVICE_ID} ${messages.m.EMPTY}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('api is duplicate', (done) => {
      try {
        request
          .post('/api/apis')
          .set('Authorization', `Bearer ${token}`)
          .send(api)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.UNIQUE_CONSTRAINT_ERROR}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('apis is required', (done) => {
      try {
        delete api[0].apis

        request
          .post('/api/apis')
          .set('Authorization', `Bearer ${token}`)
          .send(api)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.APIS} ${messages.m.REQUIRED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('apis must be array', (done) => {
      try {
        api[0].apis = 5475844

        request
          .post('/api/apis')
          .set('Authorization', `Bearer ${token}`)
          .send(api)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.APIS} ${messages.m.MUST_BE_ARRAY}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('apis can not be null', (done) => {
      try {
        api[0].apis = null

        request
          .post('/api/apis')
          .set('Authorization', `Bearer ${token}`)
          .send(api)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.APIS} ${messages.m.MUST_BE_ARRAY}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('methods must be in array', (done) => {
      try {
        api[0].apis = [{
          method: 'POST'
        }]

        console.log(api);

        request
          .post('/api/apis')
          .set('Authorization', `Bearer ${token}`)
          .send(api)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.METHOD} ${messages.m.MUST_BE_ARRAY}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('methods must be GET or POST', (done) => {
      try {
        api[0].apis = [{
          method: ['patch']
        }]

        request
          .post('/api/apis')
          .set('Authorization', `Bearer ${token}`)
          .send(api)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.METHOD_VALID_FORMAT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('methods property is required', (done) => {
      try {
        api[0].apis = [{}]

        request
          .post('/api/apis')
          .set('Authorization', `Bearer ${token}`)
          .send(api)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.METHOD} ${messages.m.REQUIRED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('path is required', (done) => {
      try {
        api[0].apis = [{
          method: ['post']
        }]

        request
          .post('/api/apis')
          .set('Authorization', `Bearer ${token}`)
          .send(api)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.PATH} ${messages.m.REQUIRED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('path must be string', (done) => {
      try {
        api[0].apis = [{
          method: ['post'],
          path: 195.1684525
        }]

        request
          .post('/api/apis')
          .set('Authorization', `Bearer ${token}`)
          .send(api)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.PATH} ${messages.m.INCORRECT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('path should not be empty', (done) => {
      try {
        api[0].apis = [{
          method: ['post'],
          path: ''
        }]

        request
          .post('/api/apis')
          .set('Authorization', `Bearer ${token}`)
          .send(api)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.PATH} ${messages.m.EMPTY}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('path can not be null value', (done) => {
      try {
        api[0].apis = [{
          method: ['post'],
          path: null
        }]

        request
          .post('/api/apis')
          .set('Authorization', `Bearer ${token}`)
          .send(api)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.PATH} ${messages.m.INCORRECT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('policies must be an object', (done) => {
      try {
        api[0].policies = []

        request
          .post('/api/apis')
          .set('Authorization', `Bearer ${token}`)
          .send(api)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.POLICIES} ${messages.m.INCORRECT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('policies can not be null value', (done) => {
      try {
        api[0].policies = null

        request
          .post('/api/apis')
          .set('Authorization', `Bearer ${token}`)
          .send(api)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.POLICIES} ${messages.m.INCORRECT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('unauthorized user can not create a user', (done) => {
      try {
        request
          .post('/api/apis')
          .set('Authorization', `Bearer ${userToken}`)
          .send(api)
          .expect(403)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.ACCESS_DENIED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('method elements must be one of post or get', (done) => {
      try {
        const apis = [
          {
            'method': [
              'patch'
            ],
            'path': 'ocr'
          }
        ]

        api[0].apis = apis

        request
          .post('/api/apis')
          .set('Authorization', `Bearer ${token}`)
          .send(api)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.METHOD_VALID_FORMAT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('serviceId(foreign key) is not uuid', (done) => {
      try {
        api[0].ServiceId = '2ec1231a85aadc4c88ec'
        request
          .post('/api/apis')
          .set('Authorization', `Bearer ${token}`)
          .send(api)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.SERVICE_ID} ${messages.m.PATTERN}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('ApiKeyId(foreign key) is not uuid', (done) => {
      try {
        api[0].ApiKeyId = '2ec1231a85aadc4c88ec'
        request
          .post('/api/apis')
          .set('Authorization', `Bearer ${token}`)
          .send(api)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.APIKEY_ID} ${messages.m.PATTERN}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('ApiKeyId(foreign key) does not exist', (done) => {
      try {
        api[0].ApiKeyId = '2ec1231a-c3f8-4fcf-bbe0-85aadc4c88ec'
        request
          .post('/api/apis')
          .set('Authorization', `Bearer ${token}`)
          .send(api)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.FOREIGN_KEY_CONSTRAINT_ERROR}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('ServiceId(foreign key) does not exist', (done) => {
      try {
        api[0].ServiceId = '2ec1231a-c3f8-4fcf-bbe0-85aadc4c88ec'
        request
          .post('/api/apis')
          .set('Authorization', `Bearer ${token}`)
          .send(api)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.FOREIGN_KEY_CONSTRAINT_ERROR}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('TPM is required', (done) => {
      try {
        delete api[0].TPM

        request
          .post('/api/apis')
          .set('Authorization', `Bearer ${token}`)
          .send(api)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.MAX_COUNT_TPM} ${messages.m.REQUIRED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('TPM must be integer', (done) => {
      try {
        api[0].TPM = 10.2

        request
          .post('/api/apis')
          .set('Authorization', `Bearer ${token}`)
          .send(api)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.MAX_COUNT_TPM} ${messages.m.PATTERN}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('TPM can not be null value', (done) => {
      try {
        api[0].TPM = null

        request
          .post('/api/apis')
          .set('Authorization', `Bearer ${token}`)
          .send(api)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.MAX_COUNT_TPM} ${messages.m.INCORRECT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('TPM must be number ', (done) => {
      try {
        api[0].TPM = '1h'

        request
          .post('/api/apis')
          .set('Authorization', `Bearer ${token}`)
          .send(api)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.MAX_COUNT_TPM} ${messages.m.INCORRECT}`
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('maxCount must be integer', (done) => {
      try {
        api[0].maxCount = 10.2

        request
          .post('/api/apis')
          .set('Authorization', `Bearer ${token}`)
          .send(api)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.MAX_COUNT} ${messages.m.PATTERN}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('maxCount must be number ', (done) => {
      try {
        api[0].maxCount = '1h'

        request
          .post('/api/apis')
          .set('Authorization', `Bearer ${token}`)
          .send(api)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.MAX_COUNT} ${messages.m.INCORRECT}`
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('maxCount can not be null value ', (done) => {
      try {
        api[0].maxCount = null

        request
          .post('/api/apis')
          .set('Authorization', `Bearer ${token}`)
          .send(api)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.MAX_COUNT} ${messages.m.INCORRECT}`
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('incorrect field', (done) => {
      try {
        api[0].UserId = 'kjshdfehjgashfjgashjf'

        request
          .post('/api/apis')
          .set('Authorization', `Bearer ${token}`)
          .send(api)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.FIELD_INCORRECT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('token is expired', (done) => {
      try {

        token =
            'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6IjNhZjZhZWIyLTg3M2EtNDNiNS04ZDM0LTE0MzczZGE2ZTFiMSIsImlhdCI6MTY2NjUwNjk3MH0.o1k__1jtkL3C2Kte'

        request
          .post('/api/apis')
          .set('Authorization', `Bearer ${token}`)
          .send(api)
          .expect(401)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.TOKEN_EXPIRED}`
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('login needed', (done) => {
      try {
        request
          .post('/api/apis')
          .send(api)
          .expect(401)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.LOGIN_NEEDED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })
  })
}
