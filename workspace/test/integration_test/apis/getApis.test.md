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



export const apiGet = () => {
  describe('GET API ', () => {
    let userId
    let token
    let userToken
    let apiId
    let service
    let superAdminId
    let adminId
    let adminToken
    let projectId
    let serviceId
    let apiKeyId

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

      apiId = await apiService.create(api)
    })

    afterAll(async () => {
      await servicesService.delete(serviceId)
      await apiService.delete(apiId)
      await apiKeyService.delete(apiKeyId)
      await usersService.delete(superAdminId)
      await usersService.delete(userId)
      await usersService.delete(adminId)
      await projectsService.delete(projectId)
    })

    it('api has been found', (done) => {
      try {
        request
          .get(`/api/apis/${apiId}`)
          .set('Authorization', `Bearer ${token}`)
          .expect(200)

          .end(function (err, res) {
            if (err) return done(err)

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

    it('admins can get apis', (done) => {
      try {
        request
          .get(`/api/apis/${apiId}`)
          .set('Authorization', `Bearer ${adminToken}`)
          .expect(200)

          .end(function (err, res) {
            if (err) return done(err)

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

    it('unauthorized user can not get an api', (done) => {
      try {
        request
          .get(`/api/apis/${apiId}`)
          .set('Authorization', `Bearer ${userToken}`)
          .expect(403)
  
          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body).toEqual(
              expect.objectContaining({
                data: expect.objectContaining({
                  message: `${messages.m.ACCESS_DENIED}`,
                  status: 'error',
                }),
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('apiId is not uuid', (done) => {
      try {
        const wrongApiId = 'kjh23j4h2jh34'

        request
          .get(`/api/apis/${wrongApiId}`)
          .set('Authorization', `Bearer ${token}`)
          .expect(400)
  
          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.ID} ${messages.m.PATTERN}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('token is expired', (done) => {
      try {
        token = 'kjh23j4h2jh34'

        request
          .get(`/api/apis/${projectId}`)
          .set('Authorization', `Bearer ${token}`)
          .expect(401)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.TOKEN_EXPIRED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })
   
    it('login needed', (done) => {
      try {
        request
          .get(`/api/apis/${apiId}`)
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
