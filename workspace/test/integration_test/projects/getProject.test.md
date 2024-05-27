import { server } from '../../../src/app.js'
import supertest from 'supertest'
const request = supertest(server)
import { randomUUID } from 'crypto'

import {
  adminPassword,
  password,
  mockPrjName2,
} from '../data/project.js'
import projectsService from '../requiredTest/projectService.js'
import messages from '../../../src/common/messages.js'
import usersService from '../requiredTest/userService.js'

export const projectGet = () => {
  describe('GET PROJECT ', () => {
    let adminId
    let projectId
    let superAdminId
    let token
    let userToken
    let adminToken
    let userId
    let project

    beforeAll(async () => {
      try {
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
          role: 'user'
        }

        userId = await usersService.create(userInfo)

        delete userInfo.role

        userToken = await usersService.login(userInfo)

        project = {
          name: mockPrjName2,
        }

        projectId = await projectsService.create(project)
      } catch (error) {
        console.log(error)
      }
    })

    afterAll(async () => {
      await projectsService.delete(projectId)
      await usersService.delete(superAdminId)
      await usersService.delete(userId)
      await usersService.delete(adminId)
    })

    it('project has been found', (done) => {
      try {
        request
          .get(`/api/projects/${projectId}`)
          .set('Authorization', `Bearer ${token}`)
          .expect(200)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'success',
                message: expect.arrayContaining([
                  expect.objectContaining({
                    id: projectId,
                    Services: [],
                    name: project.name,
                  }),
                ]),
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('admins can get projects', (done) => {
      try {
        request
          .get(`/api/projects/${projectId}`)
          .set('Authorization', `Bearer ${adminToken}`)
          .expect(200)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'success',
                message: expect.arrayContaining([
                  expect.objectContaining({
                    id: projectId,
                    Services: [],
                    name: project.name,
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
          .get(`/api/projects/${projectId}`)
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

    it('projectId is not uuid', (done) => {
      try {
        let wrongProjectId = 'kjh23j4h2jh34'

        request
          .get(`/api/projects/${wrongProjectId}`)
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
          .get(`/api/projects/${projectId}`)
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
          .get(`/api/projects/${projectId}`)
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
