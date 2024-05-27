import { server } from '../../../src/app.js'
import supertest from 'supertest'
const request = supertest(server)
import { randomUUID } from 'crypto'


import {
  adminPassword,
  mockApi,
  mockQueue,
  mockValidation,
  password,
} from '../data/service.js'
import servicesService from '../requiredTest/servicesService.js'
import usersService from '../requiredTest/userService.js'
import projectsService from '../requiredTest/projectService.js'
import messages from '../../../src/common/messages.js'

export const serviceGet = () => {
  describe('GET SERVICE ', () => {
    let adminToken
    let adminId
    let serviceId
    let projectId
    let token
    let userToken
    let superAdminId
    let userId
    let service

    beforeAll(async () => {
      const superAdminInfo = {
        username: randomUUID(),
        password: adminPassword,
        role: 'superAdmin'
      }

      superAdminId = await usersService.create(superAdminInfo)

      delete superAdminInfo.role

      token = await usersService.login(superAdminInfo)

      const adminInfo = {
        username: randomUUID(),
        password: adminPassword,
        role: 'admin'
      }

      adminId = await usersService.create(adminInfo)

      delete adminInfo.role

      adminToken = await usersService.login(adminInfo)

      const userInfo = {
        username: randomUUID(),
        password: password,
        role: 'user'
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
    })

    afterAll(async () => {
      await servicesService.delete(serviceId)
      await usersService.delete(superAdminId)
      await usersService.delete(userId)
      await usersService.delete(adminId)
      await projectsService.delete(projectId)
    })


    it('service has been found', (done) => {
      try {
        request
          .get(`/api/services/${serviceId}`)
          .set('Authorization', `Bearer ${token}`)
          .expect(200)

          .end(function (err, res) {
            if (err) return done(err)
            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'success',
                message: expect.arrayContaining([
                  expect.objectContaining({
                    id: serviceId,
                  }),
                ]),
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('admins have access to get projects', (done) => {
      try {
        request
          .get(`/api/services/${serviceId}`)
          .set('Authorization', `Bearer ${adminToken}`)
          .expect(200)

          .end(function (err, res) {
            if (err) return done(err)
            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'success',
                message: expect.arrayContaining([
                  expect.objectContaining({
                    id: serviceId,
                  }),
                ]),
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('unauthorized user can not get a project', (done) => {
      try {
        request
          .get(`/api/services/${serviceId}`)
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

    it('ProjectId is not uuid', (done) => {
      try {
        const wrongServiceId = 'kjh23j4h2jh34'

        request
          .get(`/api/services/${wrongServiceId}`)
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

    it('token is expired',(done) => {
      try {
        token = 'kjh23j4h2jh34'

        request
          .get(`/api/projects/${serviceId}`)
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
          .get(`/api/services/${serviceId}`)
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
