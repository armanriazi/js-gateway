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
import { mockExpireDate, mockUserId2 } from '../data/apikey.js'
import apiService from '../requiredTest/apiService.js'
import servicesService from '../requiredTest/servicesService.js'
import apiKeyService from '../requiredTest/apiKeyService.js'
import usersService from '../requiredTest/userService.js'
import projectsService from '../requiredTest/projectService.js'


export const apiDelete = () => {
  describe('DELETE API ', () => {
    let apiId
    let service
    let serviceId
    let token
    let userToken
    let apiKey
    let apiKeyId
    let superAdminId
    let adminId
    let adminToken
    let userId
    let projectId
    let apiKeyId2
    let apiId2

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

    it('service has been removed successfully', (done) => {
      try {
        request
          .delete(`/api/apis/${apiId}`)
          .set('Authorization', `Bearer ${token}`)
          .expect(200)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'success',
                message: 1,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('admins can delete projects', (done) => {
      try {
        request
          .delete(`/api/apis/${apiId2}`)
          .set('Authorization', `Bearer ${adminToken}`)
          .expect(200)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'success',
                message: 1,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('unauthorized user can not delete a service', (done)=> {
      try {
        request
          .delete(`/api/apis/${apiId}`)
          .set('Authorization', `Bearer ${userToken}`)
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

    it('apiId is not uuid',(done) => {
      try {
        let wrongApiId = 'kjh23j4h2jh34'

        request
          .delete(`/api/apis/${wrongApiId}`)
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

        token =
            'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6IjNhZjZhZWIyLTg3M2EtNDNiNS04ZDM0LTE0MzczZGE2ZTFiMSIsImlhdCI6MTY2NjUwNjk3MH0.o1k__1jtkL3C2Kte'

        request
          .delete(`/api/apis/${apiId}`)
          .set('Authorization', `Bearer ${token}`)
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
          .delete(`/api/apis/${apiId}`)
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
