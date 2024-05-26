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

import apiService from '../requiredTest/apiService.js'
import servicesService from '../requiredTest/servicesService.js'
import apiKeyService from '../requiredTest/apiKeyService.js'
import usersService from '../requiredTest/userService.js'
import projectsService from '../requiredTest/projectService.js'
import messages from '../../../src/common/messages.js'
import { mockExpireDate } from '../data/apikey.js'


export const apiUpdate = () => {
  describe('UPDATE API ', () => {
    let userId
    let token
    let userToken
    let service
    let superAdminId
    let adminId
    let adminToken
    let projectId
    let serviceId
    let apiKeyId
    let apiId
    let apiId2
    let apiKeyId2
    let upd_api

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

      const apiKey = {
        UserId: userId,
        maxCount: mockMaxCount,
        expireDate: mockExpireDate,
      }

      apiKeyId = await apiKeyService.create(apiKey)

      apiKeyId2 = await apiKeyService.create(apiKey)

      const api = [
        {
          ApiKeyId: apiKeyId,
          ServiceId: serviceId,
          apis: mockApis,
          TPM: mockMaxCountTpm,
          policies: mockServiceValidation,
          maxCount: 100,
        },
      ]

      const api2 = [
        {
          ApiKeyId: apiKeyId2,
          ServiceId: serviceId,
          apis: mockApis,
          TPM: mockMaxCountTpm,
          policies: mockServiceValidation,
          maxCount: 100,
        },
      ]

      apiId = await apiService.create(api)

      apiId2 = await apiService.create(api2)
    })

    afterAll(async () => {
      await servicesService.delete(serviceId)
      await apiService.delete(apiId)
      await apiService.delete(apiId2)
      await apiKeyService.delete(apiKeyId)
      await apiKeyService.delete(apiKeyId2)
      await usersService.delete(superAdminId)
      await usersService.delete(userId)
      await usersService.delete(adminId)
      await projectsService.delete(projectId)
    })

    beforeEach(() => {
      upd_api =
        {
          ApiKeyId: apiKeyId,
          ServiceId: serviceId,
          apis: mockApis,
          TPM: mockMaxCountTpm,
          policies: mockServiceValidation,
          maxCount: 100,
        }
    })

    it('update service completed successfully', (done) => {
      try {
        upd_api.maxCount = 110

        request
          .patch(`/api/apis/${apiId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(upd_api)
          .expect(200)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'success',
                message: [1],
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('admins can update api', (done) => {
      try {
        request
          .patch(`/api/apis/${apiId}`)
          .set('Authorization', `Bearer ${adminToken}`)
          .send(upd_api)
          .expect(200)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'success',
                message: [1],
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('incorrect field', (done) => {
      try {
        upd_api.field = mockApi

        request
          .patch(`/api/apis/${apiId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(upd_api)
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

    it('ApiKeyId must be string', (done) => {
      try {
        upd_api.ApiKeyId = 5464654

        request
          .patch(`/api/apis/${apiId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(upd_api)
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

    it('ApiKeyId can not be empty', (done) => {
      upd_api.ApiKeyId = ''

      request
        .patch(`/api/apis/${apiId}`)
        .set('Authorization', `Bearer ${token}`)
        .send(upd_api)
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
    })

    it('ApiKeyId should not be empty', (done) => {
      try {
        upd_api.ApiKeyId = ''

        request
          .patch(`/api/apis/${apiId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(upd_api)
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
        upd_api.ApiKeyId = null

        request
          .patch(`/api/apis/${apiId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(upd_api)
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

    it('ServiceId must be string', (done) => {
      try {
        upd_api.ServiceId = 876765767

        request
          .patch(`/api/apis/${apiId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(upd_api)
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

    it('ServiceId can not be empty', (done) => {
      upd_api.ServiceId = ''

      request
        .patch(`/api/apis/${apiId}`)
        .set('Authorization', `Bearer ${token}`)
        .send(upd_api)
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
    })

    it('ServiceId can not be null value', (done) => {
      try {
        upd_api.ServiceId = null

        request
          .patch(`/api/apis/${apiId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(upd_api)
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

    it('api is duplicate', (done) => {
      try {
        request
          .patch(`/api/apis/${apiId2}`)
          .set('Authorization', `Bearer ${token}`)
          .send(upd_api)
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

    it('apis must be array', (done) => {
      try {
        upd_api.apis = 5475844

        request
          .patch(`/api/apis/${apiId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(upd_api)
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
        upd_api.apis = null

        request
          .patch(`/api/apis/${apiId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(upd_api)
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

        upd_api.apis = apis

        request
          .patch(`/api/apis/${apiId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(upd_api)
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

    it('methods must be in array', (done) => {
      try {
        upd_api.apis = [{
          method: 'POST'
        }]

        request
          .patch(`/api/apis/${apiId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(upd_api)
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
        upd_api.apis = [{
          method: ['patch']
        }]

        request
          .patch(`/api/apis/${apiId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(upd_api)
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

    it('path must be string', (done) => {
      try {
        upd_api.apis = [{
          method: ['post'],
          path: 195.1684525
        }]

        request
          .patch(`/api/apis/${apiId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(upd_api)
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
        upd_api.apis = [{
          method: ['post'],
          path: ''
        }]

        request
          .patch(`/api/apis/${apiId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(upd_api)
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
        upd_api.apis = [{
          method: ['post'],
          path: null
        }]

        request
          .patch(`/api/apis/${apiId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(upd_api)
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
        upd_api.policies = []

        request
          .patch(`/api/apis/${apiId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(upd_api)
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
        upd_api.policies = null

        request
          .patch(`/api/apis/${apiId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(upd_api)
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

    it('ApiKeyId(foreign key) does not exist', (done) => {
      try {
        upd_api.ApiKeyId = '2ec1231a-c3f8-4fcf-bbe0-85aadc4c88ec'
        request
          .patch(`/api/apis/${apiId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(upd_api)
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
        upd_api.ServiceId = '2ec1231a-c3f8-4fcf-bbe0-85aadc4c88ec'
        request
          .patch(`/api/apis/${apiId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(upd_api)
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

    it('TPM must be integer', (done) => {
      try {
        upd_api.TPM = 10.2

        request
          .patch(`/api/apis/${apiId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(upd_api)
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
        upd_api.TPM = null

        request
          .patch(`/api/apis/${apiId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(upd_api)
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
        upd_api.TPM = '1h'

        request
          .patch(`/api/apis/${apiId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(upd_api)
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
        upd_api.maxCount = 10.2

        request
          .patch(`/api/apis/${apiId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(upd_api)
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
        upd_api.maxCount = '1h'

        request
          .patch(`/api/apis/${apiId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(upd_api)
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

    it('maxCount can not be null value', (done) => {
      try {
        upd_api.maxCount = null

        request
          .patch(`/api/apis/${apiId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(upd_api)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.MAX_COUNT} ${messages.m.INCORRECT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('unauthorized user can not update a service', (done) => {
      try {
        request
          .patch(`/api/apis/${apiId}`)
          .set('Authorization', `Bearer ${userToken}`)
          .send(upd_api)
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

    it('token is expired', (done) => {
      try {

        token =
            'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6IjNhZjZhZWIyLTg3M2EtNDNiNS04ZDM0LTE0MzczZGE2ZTFiMSIsImlhdCI6MTY2NjUwNjk3MH0.o1k__1jtkL3C2Kte'

        request
          .patch(`/api/apis/${apiId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(upd_api)
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
          .patch(`/api/apis/${apiId}`)
          .send(upd_api)
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
